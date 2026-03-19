# GitHub Repository Ecosystem (v2)

Visual representation of the Budget Analyzer organization structure.

**Replaces**: [ecosystem-overview.md](ecosystem-overview.md) (listed token-validation-service, missing ext-authz and other repos)

```
┌──────────────────────────────────────────────────────────────────────┐
│  GitHub Organization: budgetanalyzer                                  │
│                                                                       │
│  ┌──────────────────────┐      ┌──────────────────────────────────┐  │
│  │  META REPOS           │      │  SYSTEM REPOS                    │  │
│  │                       │      │                                  │  │
│  │  ┌────────────────┐  │      │  ┌──────────────────────────┐   │  │
│  │  │ .github        │  │      │  │  orchestration           │   │  │
│  │  │ (org templates)│  │      │  │  CLAUDE.md               │   │  │
│  │  └────────────────┘  │      │  │  - System patterns       │   │  │
│  │                       │      │  │  - Gateway config        │   │  │
│  │  ┌────────────────┐  │      │  │  - K8s manifests         │   │  │
│  │  │ architecture-  │  │      │  │  - ext_authz source      │   │  │
│  │  │ conversations  │  │      │  │  - NGINX config          │   │  │
│  │  │ (THIS REPO)    │  │      │  └──────────────────────────┘   │  │
│  │  │                │  │      │                                  │  │
│  │  │ Conversations  │  │      │  ┌──────────────────────────┐   │  │
│  │  │ about all      │  │      │  │  service-common          │   │  │
│  │  │ repos ─────────┼──┼──────┼─→│  CLAUDE.md               │   │  │
│  │  └────────────────┘  │      │  │  - Spring patterns       │   │  │
│  │                       │      │  │  - Shared library        │   │  │
│  │  ┌────────────────┐  │      │  └──────────────────────────┘   │  │
│  │  │ workspace      │  │      │                                  │  │
│  │  │ (devcontainer  │  │      │  ┌──────────────────────────┐   │  │
│  │  │  entry point)  │  │      │  │  checkstyle-config       │   │  │
│  │  └────────────────┘  │      │  │  - Shared checkstyle     │   │  │
│  │                       │      │  └──────────────────────────┘   │  │
│  └──────────────────────┘      └──────────────────────────────────┘  │
│                                                                       │
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │  GATEWAY REPOS (Auth + Routing)                                │  │
│  │                                                                 │  │
│  │  ┌──────────────┐  ┌──────────────┐                           │  │
│  │  │ session      │  │ ext-authz    │                           │  │
│  │  │ -gateway     │  │ (in orch.)   │                           │  │
│  │  │ CLAUDE.md    │  │ Go HTTP svc  │                           │  │
│  │  │ - BFF/OAuth  │  │ - Session    │                           │  │
│  │  │ - Session    │  │   validation │                           │  │
│  │  │   lifecycle  │  │ - Redis      │                           │  │
│  │  │ - Dual-write │  │   lookup     │                           │  │
│  │  │   to Redis   │  │ - Header     │                           │  │
│  │  └──────────────┘  │   injection  │                           │  │
│  │                     └──────────────┘                           │  │
│  └────────────────────────────────────────────────────────────────┘  │
│                                                                       │
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │  SERVICE REPOS (Microservices)                                  │  │
│  │                                                                 │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │  │
│  │  │ transaction  │  │ currency     │  │ permission   │        │  │
│  │  │ -service     │  │ -service     │  │ -service     │        │  │
│  │  │ CLAUDE.md    │  │ CLAUDE.md    │  │ CLAUDE.md    │        │  │
│  │  │ - Thin       │  │ - Thin       │  │ - Thin       │        │  │
│  │  │ - References │  │ - References │  │ - References │        │  │
│  │  │   common     │  │   common     │  │   common     │        │  │
│  │  └──────────────┘  └──────────────┘  └──────────────┘        │  │
│  └────────────────────────────────────────────────────────────────┘  │
│                                                                       │
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │  FRONTEND REPOS                                                │  │
│  │                                                                 │  │
│  │  ┌──────────────────────────────────────┐                     │  │
│  │  │  budget-analyzer-web                  │                     │  │
│  │  │  CLAUDE.md                            │                     │  │
│  │  │  - React patterns                     │                     │  │
│  │  │  - API integration through gateway    │                     │  │
│  │  └──────────────────────────────────────┘                     │  │
│  └────────────────────────────────────────────────────────────────┘  │
│                                                                       │
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │  EXPERIMENTAL                                                   │  │
│  │                                                                 │  │
│  │  ┌──────────────┐                                             │  │
│  │  │ claude-      │                                             │  │
│  │  │ discovery    │                                             │  │
│  │  │ (experiment) │                                             │  │
│  │  └──────────────┘                                             │  │
│  └────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘

KEY:
CLAUDE.md = AI context file
─→ = References/depends on
```

## Repository Types

### Meta Repositories
- **.github**: Organization-level templates and configurations
- **architecture-conversations**: This repository - architectural discourse
- **workspace**: Devcontainer entry point (clone this first for full dev environment)

### System Repositories
- **orchestration**: Deployment coordination, NGINX config, ext_authz source, K8s manifests, system-wide docs
- **service-common**: Shared Java library with Spring Boot patterns
- **checkstyle-config**: Shared code style rules

### Gateway Repositories
- **session-gateway**: BFF for browser authentication, OAuth2 flows, session lifecycle, dual-write to ext_authz Redis
- **ext-authz** (in orchestration): Go HTTP service for per-request session validation at the Envoy layer

### Microservice Repositories
- **transaction-service**: Transaction management API
- **currency-service**: Currency and exchange rate API
- **permission-service**: Internal roles/permissions resolution (called by Session Gateway)

### Frontend Repositories
- **budget-analyzer-web**: React-based web application

### Experimental
- **claude-discovery**: Experimental discovery tool

## Service Inventory

| Service | Port | Language | Purpose |
|---------|------|----------|---------|
| Envoy Gateway | 443 | - | SSL termination, ext_authz enforcement |
| ext_authz | 9002 | Go | Per-request session validation |
| NGINX Gateway | 8080 | - | API routing, rate limiting |
| Session Gateway | 8081 | Java | BFF, OAuth2, session lifecycle |
| Transaction Service | 8082 | Java | Transaction management |
| Currency Service | 8084 | Java | Exchange rates |
| Permission Service | 8086 | Java | Roles/permissions resolution |
| React Dev Server | 3000 | TypeScript | Frontend (dev only) |

## CLAUDE.md Distribution

Every service repository contains a CLAUDE.md file that:
- Provides AI context specific to that service
- References service-common for inherited patterns
- Uses discovery commands instead of hardcoded lists
- Maintains thin files with references to detailed docs

## AI Navigation Pattern

1. Start at `orchestration/CLAUDE.md` (system overview)
2. Discover all services via Tiltfile/K8s manifests
3. Navigate to specific service CLAUDE.md for focused work
4. Load service-common context for inherited patterns
5. Just-in-time load detailed docs as needed
