# Request Flow Through BFF + API Gateway (v2)

Visual representation of how browser requests flow through the ext_authz-based architecture.

**Replaces**: [request-flow-bff-gateway.md](request-flow-bff-gateway.md) (described pre-ext_authz JWT-based flow)

```
BROWSER REQUEST THROUGH EXT_AUTHZ ARCHITECTURE
════════════════════════════════════════════════

Browser makes request to: https://app.budgetanalyzer.localhost/api/v1/transactions

   ┌─────────┐
   │ Browser │
   └────┬────┘
        │ HTTPS (443)
        │ Cookie: SESSION=abc123
        │ NO tokens (XSS protection!)
        ▼
   ┌────────────────────────────────────────┐
   │  Envoy Gateway (:443)                  │
   │  - SSL termination                     │
   │  - ext_authz enforcement on /api/*     │
   │  - Routes /auth/*, /login/*, /logout   │
   │    → Session Gateway                   │
   │  - Routes /api/*, /* → NGINX           │
   └─────────────────┬─────────────────────┘
                     │
                     │ ext_authz callout (every /api/* request)
                     ▼
   ┌────────────────────────────────────────┐
   │  ext_authz Service (:9002)             │
   │  1. Extract session ID from cookie     │
   │  2. Redis lookup: extauthz:session:{id}│
   │  3. If valid: inject headers            │
   │     X-User-Id, X-Roles, X-Permissions  │
   │  4. If invalid: return 401 (rejected)  │
   └─────────────────┬─────────────────────┘
                     │
                     │ HTTP (8080)
                     │ X-User-Id: user-abc-123
                     │ X-Roles: ROLE_USER
                     │ X-Permissions: transactions:read
                     ▼
   ┌────────────────────────────────────────┐
   │  NGINX API Gateway (:8080)             │
   │  1. Route /api/v1/transactions →       │
   │     transaction-service                │
   │  2. Rate limiting                      │
   │  3. No auth logic (pre-validated)      │
   └─────────────────┬─────────────────────┘
                     │ HTTP (8082)
                     │ X-User-Id: user-abc-123
        ┌────────────┼────────────┐
        │            │            │
        ▼            ▼            ▼
   ┌─────────┐ ┌─────────┐ ┌──────────┐
   │ Trans   │ │Currency │ │Permission│
   │ Service │ │ Service │ │ Service  │
   │ (:8082) │ │ (:8084) │ │ (:8086)  │
   └─────────┘ └─────────┘ └──────────┘
```

## Auth Lifecycle (Separate Path)

```
AUTHENTICATION FLOW (login, logout, token refresh)
═══════════════════════════════════════════════════

   ┌─────────┐
   │ Browser │
   └────┬────┘
        │ HTTPS (443)
        │ /login, /auth/*, /logout
        ▼
   ┌────────────────────────────────────────┐
   │  Envoy Gateway (:443)                  │
   │  - No ext_authz on auth paths          │
   │  - Routes directly to Session Gateway  │
   └─────────────────┬─────────────────────┘
                     │ HTTP (8081)
                     ▼
   ┌────────────────────────────────────────┐
   │  Session Gateway (BFF) (:8081)         │
   │  1. Manages OAuth2 flows with Auth0    │
   │  2. Stores Auth0 tokens in Redis       │
   │  3. Calls permission-service (:8086)   │
   │     to resolve roles/permissions       │
   │  4. Dual-writes session data to        │
   │     ext_authz Redis schema             │
   │  5. Sets HttpOnly session cookie       │
   └────────────────────────────────────────┘
```

## Key Differences from v1 Architecture

| Aspect | v1 (Old) | v2 (Current) |
|--------|----------|--------------|
| Session validation | Session Gateway in request path | ext_authz at Envoy layer |
| Identity propagation | JWT in Authorization header | X-User-Id, X-Roles, X-Permissions headers |
| NGINX auth | `auth_request` to token-validation-service | No auth logic; receives pre-validated requests |
| Token Validation Service | Separate service (:8088) | Removed; ext_authz replaces it |
| Session Gateway role | Proxied all API requests | Auth lifecycle only (/auth/*, /login/*, /logout) |
| Entry points | Two hosts (app.*, api.*) | Single host (app.budgetanalyzer.localhost) |
| Revocation | JWT expiry window | Instant via Redis key deletion |

## Key Security Benefits

### 1. Tokens Never Exposed to Browser
- Browser only receives session cookie (HttpOnly, Secure, SameSite=Strict)
- Auth0 tokens stored in Redis by Session Gateway
- ext_authz validates sessions via Redis lookup - no tokens on the wire

### 2. Instant Session Revocation
- Delete Redis key `extauthz:session:{id}` → next request immediately rejected
- No JWT expiry window to wait out
- Bulk revocation possible for security incidents

### 3. Per-Request Validation at the Edge
- ext_authz validates every `/api/*` request before it reaches NGINX
- Requests rejected at Envoy layer, never touching backend services
- Anti-spoofing: Envoy's `headersToBackend` allowlist ensures only ext_authz-injected headers reach backends

### 4. No CORS Issues
- All requests through single origin (app.budgetanalyzer.localhost)
- Envoy routes internally to Session Gateway or NGINX
- Browser sees single origin = no CORS preflight overhead

### 5. Resource-Based Routing
- Frontend requests `/api/v1/transactions` (resource)
- NGINX routes to appropriate service
- Service boundaries can change without frontend changes

## Request Flow Details

### Step 1: Browser Request
```http
GET https://app.budgetanalyzer.localhost/api/v1/transactions HTTP/1.1
Host: app.budgetanalyzer.localhost
Cookie: SESSION=abc123-def456-ghi789
```

### Step 2: Envoy Gateway (SSL Termination + ext_authz)
```
Terminates SSL
Path matches /api/* → triggers ext_authz callout
Calls ext_authz service at :9002
```

### Step 3: ext_authz Service (Session Validation)
```
Extracts session ID from cookie
Redis: HGETALL extauthz:session:abc123-def456-ghi789
Returns fields: user_id, roles, permissions, created_at, expires_at
If valid: sets response headers (X-User-Id, X-Roles, X-Permissions)
If invalid/missing: returns 401 (Envoy rejects the request)
```

### Step 4: Envoy Forwards to NGINX
```
Applies headersToBackend allowlist (anti-spoofing)
Only X-User-Id, X-Roles, X-Permissions forwarded
Routes to NGINX at :8080
```

### Step 5: NGINX API Gateway (Routing)
```nginx
location /api/v1/transactions {
    # No auth logic - request is pre-validated by ext_authz
    set $backend "http://transaction-service.default.svc.cluster.local:8082";
    proxy_pass $backend;
    # Identity headers already present from ext_authz
}
```

### Step 6: Transaction Service
```
Reads X-User-Id header: "user-abc-123"
Reads X-Permissions header: "transactions:read"
Scopes query: WHERE user_id = 'user-abc-123'
Returns response
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

### BFF + ext_authz (Budget Analyzer)

```
Browser → Envoy → ext_authz → NGINX → Services
   │
   ├─ Session cookie only (HttpOnly, Secure, SameSite)
   ├─ No CORS needed (same-origin)
   ├─ Server manages tokens (Redis)
   └─ Instant revocation (Redis key delete)
```

## Why Three Layers?

### Why ext_authz? (Not just Session Gateway)
- Session Gateway manages auth lifecycle (login, token refresh, logout)
- ext_authz validates every API request without touching Session Gateway
- Separation of concerns: auth management vs auth enforcement
- ext_authz is stateless (Redis lookup only) = fast and scalable

### Why NGINX? (Not just Envoy)
- Envoy handles ingress, SSL, and ext_authz enforcement
- NGINX handles routing, rate limiting, load balancing
- Each layer does one thing well
- NGINX config is simpler to maintain for routing rules

### Why Session Gateway? (Not just ext_authz)
- ext_authz can't manage OAuth2 flows or Auth0 integration
- Session Gateway handles the complex auth lifecycle
- Writes session data that ext_authz reads
- Manages token refresh, permission enrichment

## Configuration Snippets

### Envoy Gateway (SecurityPolicy)
```yaml
apiVersion: gateway.envoy.proxy/v1alpha1
kind: SecurityPolicy
spec:
  extAuth:
    http:
      backendRef:
        name: ext-authz
        port: 9002
      headersToBackend:
        - X-User-Id
        - X-Roles
        - X-Permissions
```

### ext_authz Redis Schema
```
Key:    extauthz:session:{session-id}
Type:   Hash
Fields: user_id, roles, permissions, created_at, expires_at
TTL:    30 minutes (matches Spring Session)
```

### NGINX API Gateway (No Auth Logic)
```nginx
location /api/v1/transactions {
    set $backend "http://transaction-service.default.svc.cluster.local:8082";
    proxy_pass $backend;
    # Headers X-User-Id, X-Roles, X-Permissions
    # already injected by ext_authz via Envoy
}
```

## Production Evidence

**Budget Analyzer Implementation**:
- Session Gateway: https://github.com/budgetanalyzer/session-gateway
- ext_authz: Go HTTP service in orchestration repo
- NGINX Config: https://github.com/budgetanalyzer/orchestration/blob/main/nginx/nginx.k8s.conf
- Architecture Docs: https://github.com/budgetanalyzer/orchestration/blob/main/docs/architecture/bff-api-gateway-pattern.md
- Security Architecture: https://github.com/budgetanalyzer/orchestration/blob/main/docs/architecture/security-architecture.md

## References

- BFF + API Gateway Pattern: [orchestration/docs/architecture/bff-api-gateway-pattern.md](https://github.com/budgetanalyzer/orchestration/blob/main/docs/architecture/bff-api-gateway-pattern.md)
- Security Architecture: [orchestration/docs/architecture/security-architecture.md](https://github.com/budgetanalyzer/orchestration/blob/main/docs/architecture/security-architecture.md)
- Port Reference: [orchestration/docs/architecture/port-reference.md](https://github.com/budgetanalyzer/orchestration/blob/main/docs/architecture/port-reference.md)
