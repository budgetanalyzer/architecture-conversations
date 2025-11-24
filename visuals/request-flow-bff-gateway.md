# Request Flow Through BFF + API Gateway

Visual representation of how browser requests flow through the dual-gateway architecture.

```
BROWSER REQUEST THROUGH DUAL-GATEWAY ARCHITECTURE
══════════════════════════════════════════════════

Browser makes request to: https://app.budgetanalyzer.localhost/api/v1/transactions

   ┌─────────┐
   │ Browser │
   └────┬────┘
        │ HTTPS (443)
        │ Cookie: SESSION=abc123
        │ NO JWT (XSS protection!)
        ▼
   ┌────────────────────────────────────────┐
   │  Envoy Gateway (:443)                   │
   │  - SSL termination                      │
   │  - Route: app.* → Session Gateway       │
   └─────────────────┬──────────────────────┘
                     │ HTTP (8081)
                     │ Cookie: SESSION=abc123
                     ▼
   ┌────────────────────────────────────────┐
   │  Session Gateway (BFF) (:8081)          │
   │  1. Lookup session in Redis             │
   │  2. Retrieve JWT for session            │
   │  3. Inject Authorization header         │
   │  4. Forward to backend                  │
   └─────────────────┬──────────────────────┘
                     │ HTTP (via Envoy)
                     │ Authorization: Bearer eyJ...
                     │ (JWT added by Session Gateway)
                     ▼
   ┌────────────────────────────────────────┐
   │  Envoy Gateway (:443 internal)          │
   │  - Route: api.* → NGINX Gateway         │
   └─────────────────┬──────────────────────┘
                     │ HTTP (8080)
                     │ Authorization: Bearer eyJ...
                     ▼
   ┌────────────────────────────────────────┐
   │  NGINX API Gateway (:8080)              │
   │  1. auth_request → Token Validation     │
   │  2. Validate JWT (exp, signature, etc)  │
   │  3. Route /api/v1/transactions →        │
   │     transaction-service                 │
   └─────────────────┬──────────────────────┘
                     │ HTTP (8082)
                     │ Authorization: Bearer eyJ...
        ┌────────────┼────────────┐
        │            │            │
        ▼            ▼            ▼
   ┌─────────┐ ┌─────────┐ ┌─────────┐
   │ Trans   │ │Currency │ │Permission│
   │ Service │ │ Service │ │ Service │
   │ (:8082) │ │ (:8084) │ │ (:8086) │
   └─────────┘ └─────────┘ └─────────┘
```

## Key Security Benefits

### 1. JWT Never Exposed to Browser
**Problem**: JWTs in browser localStorage/sessionStorage are vulnerable to XSS attacks

**Solution**:
- Browser only receives session cookie (HttpOnly, Secure)
- Session Gateway stores JWT in Redis
- JWT only travels server-to-server

**Protection**: XSS attacker can't steal JWT from browser

### 2. No CORS Issues
**Problem**: Traditional SPA + API requires CORS configuration

**Solution**:
- All requests go through same-origin (app.budgetanalyzer.localhost)
- Session Gateway proxies to backend services
- Browser sees single origin

**Benefit**: No CORS preflight overhead, simpler security model

### 3. Centralized JWT Validation
**Problem**: Each microservice shouldn't validate JWTs independently

**Solution**:
- NGINX API Gateway validates JWT via Token Validation Service
- Single source of truth for validation logic
- Services trust NGINX's validation

**Benefit**: Defense in depth, consistent validation

### 4. Resource-Based Routing
**Problem**: Frontend shouldn't know service topology

**Solution**:
- Frontend requests `/api/v1/transactions` (resource)
- NGINX routes to appropriate service
- Service boundaries can change without frontend changes

**Benefit**: Loose coupling, easier refactoring

## Two Entry Points

### Entry Point 1: Browser Requests (via app.budgetanalyzer.localhost)

```
Browser → Envoy → Session Gateway → Envoy → NGINX → Services
         :443     :8081             :443    :8080    :808x

Security: Session cookies, JWT injected by Session Gateway
Use case: User-facing web application
```

### Entry Point 2: Direct API Requests (via api.budgetanalyzer.localhost)

```
API Client → Envoy → NGINX → Services
            :443     :8080    :808x

Security: JWT in Authorization header (client manages)
Use case: Service-to-service, mobile apps, CLI tools
```

## Request Flow Details

### Step 1: Browser Request
```http
GET https://app.budgetanalyzer.localhost/api/v1/transactions HTTP/1.1
Host: app.budgetanalyzer.localhost
Cookie: SESSION=abc123-def456-ghi789
```

### Step 2: Envoy Gateway (SSL Termination)
```
Terminates SSL
Routes app.* to Session Gateway :8081
Forwards as HTTP
```

### Step 3: Session Gateway (BFF)
```java
// Pseudo-code
String sessionId = extractCookie("SESSION");
String jwt = redis.get("session:" + sessionId);
request.setHeader("Authorization", "Bearer " + jwt);
proxy.forward(request);
```

### Step 4: Envoy Gateway (Internal Routing)
```
Routes to api.budgetanalyzer.localhost (NGINX)
Forwards with Authorization header
```

### Step 5: NGINX API Gateway
```nginx
location /api/v1/transactions {
    auth_request /auth;  # Validate JWT first
    proxy_pass http://transaction-service:8082;
}

location = /auth {
    proxy_pass http://token-validation-service:8088/validate;
    proxy_pass_request_body off;
}
```

### Step 6: Token Validation Service
```java
// Validates JWT
- Check signature
- Check expiration
- Check issuer
- Extract claims

// Returns 200 OK or 401 Unauthorized
```

### Step 7: Transaction Service
```java
// Receives validated request
// Trusts that NGINX already validated JWT
// Processes business logic
// Returns response
```

## Comparison with Traditional Architectures

### Traditional SPA + API

```
Browser → API Gateway → Services
   │
   ├─ JWT in localStorage (XSS risk!)
   ├─ CORS required
   └─ Client manages tokens
```

**Issues**:
- XSS can steal JWT
- CORS complexity
- Token refresh logic in frontend

### BFF + API Gateway Hybrid (Budget Analyzer)

```
Browser → Envoy → Session Gateway → Envoy → NGINX → Services
   │
   ├─ Session cookie only (HttpOnly, Secure)
   ├─ No CORS needed
   └─ Server manages tokens
```

**Benefits**:
- JWT never in browser
- Same-origin architecture
- Server-side token management

## Why Two Gateways?

### Why Not Just Session Gateway?

**Problem**: Session Gateway is browser-focused
- Manages session cookies
- Handles authentication flow
- Designed for web apps

**Need**: Service-to-service communication doesn't need sessions
- Mobile apps want to send JWT directly
- CLI tools want to send JWT directly
- Other services want to call APIs

**Solution**: NGINX handles both patterns
- Accepts JWTs from Session Gateway
- Accepts JWTs from direct clients
- Provides unified routing layer

### Why Not Just NGINX?

**Problem**: NGINX doesn't manage browser sessions
- No session storage
- No cookie management
- No authentication flow

**Need**: Browsers need session-based auth
- HttpOnly cookies for security
- Session storage for JWTs
- Authentication redirects

**Solution**: Session Gateway handles browser concerns
- Manages sessions
- Stores JWTs server-side
- Injects JWTs into requests

## Configuration Snippets

### Envoy Gateway (Gateway API)

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: app-route
spec:
  hostnames:
  - "app.budgetanalyzer.localhost"
  rules:
  - backendRefs:
    - name: session-gateway
      port: 8081
```

### Session Gateway (Spring Cloud Gateway)

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: api-route
        uri: https://api.budgetanalyzer.localhost
        predicates:
        - Path=/api/**
        filters:
        - JwtInjectionFilter  # Custom filter
```

### NGINX API Gateway

```nginx
location /api/v1/transactions {
    auth_request /auth;
    auth_request_set $auth_status $upstream_status;

    proxy_pass http://transaction-service:8082;
    proxy_set_header Authorization $http_authorization;
}

location = /auth {
    internal;
    proxy_pass http://token-validation-service:8088/validate;
    proxy_pass_request_body off;
    proxy_set_header Content-Length "";
    proxy_set_header X-Original-URI $request_uri;
}
```

## Architectural Rationale

This architecture emerged from conversation about:
**"How do we secure JWTs in a financial application?"**

Key insights:
1. Financial data requires strong XSS protection
2. JWTs should never be accessible to browser JavaScript
3. Sessions provide better browser security model
4. But services need stateless JWT validation
5. BFF + API Gateway gives us both

**See**: [architecture-conversations/conversations/006-bff-security-rationale.md](../conversations/006-bff-security-rationale.md) (future)

## Production Evidence

**Budget Analyzer Implementation**:
- Session Gateway: https://github.com/budgetanalyzer/session-gateway
- NGINX Config: https://github.com/budgetanalyzer/orchestration/blob/main/nginx/nginx.k8s.conf
- Token Validation: https://github.com/budgetanalyzer/token-validation-service
- Orchestration Docs: https://github.com/budgetanalyzer/orchestration/blob/main/docs/architecture/bff-api-gateway-pattern.md

## References

- Pattern: [patterns/bff-api-gateway-hybrid.md](../patterns/bff-api-gateway-hybrid.md) (future)
- Conversation: [conversations/006-bff-security-rationale.md](../conversations/006-bff-security-rationale.md) (future)
- Implementation: [orchestration/docs/architecture/bff-api-gateway-pattern.md](https://github.com/budgetanalyzer/orchestration/blob/main/docs/architecture/bff-api-gateway-pattern.md)
