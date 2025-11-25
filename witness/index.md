# CLAUDE.md Witness Page

## What You're About to See

This page demonstrates **AI-native architecture** - the practice of designing software systems that AI can understand and navigate through structured context files.

**The claim**: A well-structured `CLAUDE.md` file gives AI complete context about a software system. The AI doesn't need to be "trained" on the codebase - it just needs to read the context file.

**Your role as witness**: You'll paste this page's URL into a browser-based AI (Claude, ChatGPT, etc.), then ask questions about the Budget Analyzer system. The AI should answer accurately based solely on the context below.

---

## Instructions

1. **Copy this page's URL** (after GitHub Pages is enabled, it will be something like `budgetanalyzer.github.io/architecture-conversations/witness/`)

2. **Go to claude.com** (or any browser-based AI)

3. **Paste the URL** and ask Claude to fetch and read it

4. **Ask questions** like:
   - "What services are in this system?"
   - "How does authentication work?"
   - "What's the request flow from browser to backend?"
   - "How do I add a new microservice?"

5. **Observe**: The AI should answer accurately, demonstrating that structured context enables AI comprehension without training.

---

## The Context (Budget Analyzer Orchestration)

Below is the complete `CLAUDE.md` file from the Budget Analyzer orchestration repository. This is the same file that AI agents read when working on the actual codebase.

---

# Budget Analyzer - Orchestration Repository

## Project Overview

This orchestration repository coordinates the deployment and development environment for the Budget Analyzer application - a production-grade, microservices-based financial management system.

**Purpose**: Manages cross-service concerns, local development setup, and deployment coordination. Individual service code lives in separate repositories.

## Development Environment Requirements

**This project is designed for AI-assisted development. Containerized agents are the default and expected approach.**

### AI-Assisted Development (Primary Approach)

**Required**: VS Code with Dev Containers extension

**Why containerized agents are mandatory**:
- AI agents need safe sudo access (container provides isolation from host system)
- Consistent tooling pre-installed (JDK, Node.js, Maven, Docker CLI)
- Workspace-wide context (all repositories accessible under `/workspace`)

**Not supported**:
- **Cursor**: Closed source, violates open source tool policy
- **IntelliJ IDEA**: No containerized agent support
- **Any editor without containerized agent architecture**: Cannot provide safe AI execution environment

This is non-negotiable. AI agents operating outside containerized environments pose security risks and lack the consistent tooling required for this project.

### Traditional Development (Not the Focus)

You can work on this codebase without AI using any IDE (IntelliJ IDEA, VS Code, etc.), but that's the before times. This is an AI-first learning resource for architects exploring AI-assisted development.

## Architecture Principles

- **Production Parity**: Development environment faithfully recreates production
- **Microservices**: Independently deployable services with clear boundaries
- **BFF Pattern**: Session Gateway provides browser security and authentication management
- **API Gateway Pattern**: NGINX provides unified routing, JWT validation, and load balancing
- **Resource-Based Routing**: Frontend remains decoupled from service topology
- **Defense in Depth**: Multiple security layers (Session Gateway → NGINX → Services)
- **Kubernetes-Native Development**: Tilt + Kind for consistent local Kubernetes development

## Service Architecture

**Pattern**: Microservices deployed via Tilt to local Kind cluster

**Discovery**:
```bash
# List all running resources
tilt get uiresources

# View pod status
kubectl get pods -n budget-analyzer

# View service endpoints
kubectl get svc -n budget-analyzer
```

**Service Types**:
- **Frontend services**: React-based web applications (port 3000 in dev)
- **Backend microservices**: Spring Boot REST APIs (ports 8082+)
- **Session Gateway (BFF)**: Spring Cloud Gateway (port 8081, HTTP) - browser authentication and session management
- **Token Validation Service**: Spring Boot service (port 8088) - JWT validation for NGINX
- **Infrastructure**: PostgreSQL, Redis, RabbitMQ (in infrastructure namespace)
- **Ingress**: Envoy Gateway (port 443, HTTPS) - SSL termination and initial routing
- **API Gateway**: NGINX (port 8080, HTTP) - internal routing, JWT validation, and load balancing

**Adding New Services**: Create K8s manifests in `kubernetes/services/{name}/`, add to `Tiltfile`, add NGINX routes if needed.

## BFF + API Gateway Hybrid Pattern

**Pattern**: Hybrid architecture combining Backend-for-Frontend (BFF) for browser security with API Gateway for routing and validation.

**Request Flow**:
```
Browser → Envoy (:443) → Session Gateway (:8081) → Envoy → NGINX (:8080) → Services
```

**Two entry points**:
- `app.budgetanalyzer.localhost` → Session Gateway (browser auth, session cookies)
- `api.budgetanalyzer.localhost` → NGINX (JWT validation, routing)

**Key Benefits**:
- Same-origin architecture = no CORS issues
- JWTs never exposed to browser (XSS protection for financial data)
- Centralized JWT validation and routing

**Discovery**:
```bash
# List all API routes
grep "location /api" nginx/nginx.k8s.conf | grep -v "#"

# Test gateways
curl -v https://app.budgetanalyzer.localhost/actuator/health
curl -v https://api.budgetanalyzer.localhost/health

# View service ports
kubectl get svc -n budget-analyzer
```

## Technology Stack

**Principle**: Each service manages its own dependencies. Versions are defined in service-specific files.

**Discovery**:
```bash
# List all Tilt resources
tilt get uiresources

# View deployed images
kubectl get pods -n budget-analyzer -o jsonpath='{.items[*].spec.containers[*].image}' | tr ' ' '\n' | sort -u
```

**Stack Patterns**:
- **Frontend**: React (see individual service package.json)
- **Backend**: Spring Boot + Java (version managed in service-common)
- **Build System**: Gradle (all backend services use Gradle with wrapper)
- **Infrastructure**: PostgreSQL, Redis, RabbitMQ (Kubernetes manifests in `kubernetes/infrastructure/`)
- **Ingress**: Envoy Gateway (Kubernetes Gateway API)
- **API Gateway**: NGINX (Alpine-based)
- **Development**: Tilt + Kind (local Kubernetes)

**Note**: Docker images should be pinned to specific versions for reproducibility.

## Development Workflow

### Prerequisites & Setup

**Required tools**: Docker, Kind, kubectl, Helm, Tilt, Git, mkcert

Check prerequisites:
```bash
./scripts/dev/check-tilt-prerequisites.sh
```

**First-time setup**:
```bash
# 1. Generate HTTPS certificates
./scripts/dev/setup-k8s-tls.sh

# 2. Configure Auth0 credentials
cp .env.example .env
# Edit .env with your Auth0 credentials
```

### Quick Start
```bash
# Start all services with Tilt
tilt up

# Access Tilt UI for logs and status
# Browser: http://localhost:10350

# Access application
# Browser: https://app.budgetanalyzer.localhost

# Stop all services
tilt down
```

### Troubleshooting

**Quick commands**:
```bash
# Check pod status
kubectl get pods -n budget-analyzer

# View logs for a service
kubectl logs -n budget-analyzer deployment/nginx-gateway

# Check NGINX configuration validity
kubectl exec -n budget-analyzer deployment/nginx-gateway -- nginx -t

# View Envoy Gateway logs
kubectl logs -n envoy-gateway-system deployment/envoy-gateway
```

## Workspace Structure

All repositories should be cloned side-by-side in a common parent directory:

```
/workspace/
├── .github/                    # Organization-level GitHub config
├── orchestration/              # This repo - deployment coordination
├── session-gateway/            # BFF service
├── token-validation-service/   # JWT validation service
├── transaction-service/        # Transaction management
├── currency-service/           # Currency/exchange rates
├── permission-service/         # Permission management
├── budget-analyzer-web/        # React frontend
├── service-common/             # Shared Java library
└── checkstyle-config/          # Shared checkstyle rules
```

## Service Repositories

Each microservice is maintained in its own repository:
- **service-common**: Shared library for all backend services
- **transaction-service**: Transaction management API
- **currency-service**: Currency and exchange rate API
- **budget-analyzer-web**: React frontend application
- **session-gateway**: BFF for browser authentication
- **token-validation-service**: JWT validation for NGINX
- **permission-service**: Permission management API

## Best Practices

1. **Environment Parity**: Keep dev and prod configurations as similar as possible
2. **Configuration Management**: Use environment variables for configuration
3. **Health Checks**: All services expose health endpoints
4. **Service Independence**: Each microservice should be independently deployable
5. **API Versioning**: Version APIs to support backward compatibility
6. **Living Documentation**: Verify accuracy by running discovery commands

---

## What This Demonstrates

If the AI correctly answered your questions about:
- The microservices in the system (transaction, currency, permission, etc.)
- The dual-gateway architecture (Session Gateway + NGINX)
- The security model (JWTs never in browser)
- How to add new services

Then you've witnessed the core insight: **Structured context enables AI comprehension without training.**

This is the foundation of AI-native architecture:
- Each repository has a CLAUDE.md file
- AI agents read these files to understand the system
- No special training or fine-tuning required
- The context IS the knowledge

---

## Learn More

- **Full conversations**: [architecture-conversations repository](https://github.com/budgetanalyzer/architecture-conversations)
- **Live system**: [Budget Analyzer organization](https://github.com/budgetanalyzer)
- **Pattern documentation**: Context Window-Driven Design, Knowledge Externalization Protocol
