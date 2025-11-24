# GitHub Repository Ecosystem

Visual representation of the Budget Analyzer organization structure showing all repositories and their CLAUDE.md files.

```
┌─────────────────────────────────────────────────────────────────┐
│  GitHub Organization: budgetanalyzer                            │
│                                                                 │
│  ┌──────────────────┐         ┌──────────────────────────────┐│
│  │  META REPOS      │         │  SYSTEM REPOS                ││
│  │                  │         │                              ││
│  │  ┌────────────┐  │         │  ┌────────────────────────┐ ││
│  │  │ .github    │  │         │  │  orchestration         │ ││
│  │  │ (templates)│  │         │  │  CLAUDE.md ★           │ ││
│  │  └────────────┘  │         │  │  - System patterns     │ ││
│  │                  │         │  │  - Gateway config      │ ││
│  │  ┌────────────┐  │         │  │  - K8s manifests       │ ││
│  │  │ architecture│  │         │  └────────────────────────┘ ││
│  │  │-conversations│ │         │                              ││
│  │  │ (THIS REPO) │  │         │  ┌────────────────────────┐ ││
│  │  │             │  │         │  │  service-common        │ ││
│  │  │ Conversations│  │         │  │  CLAUDE.md ★           │ ││
│  │  │ about all   │  │         │  │  - Spring patterns     │ ││
│  │  │ repos ──────┼──┼─────────┼─→│  - Shared library     │ ││
│  │  └────────────┘  │         │  └────────────────────────┘ ││
│  └──────────────────┘         └──────────────────────────────┘│
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  SERVICE REPOS (Microservices)                          │  │
│  │                                                          │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │  │
│  │  │ transaction  │  │ currency     │  │ permission   │  │  │
│  │  │ -service     │  │ -service     │  │ -service     │  │  │
│  │  │ CLAUDE.md ★  │  │ CLAUDE.md ★  │  │ CLAUDE.md ★  │  │  │
│  │  │ - Thin       │  │ - Thin       │  │ - Thin       │  │  │
│  │  │ - References │  │ - References │  │ - References │  │  │
│  │  │   common     │  │   common     │  │   common     │  │  │
│  │  └──────────────┘  └──────────────┘  └──────────────┘  │  │
│  │                                                          │  │
│  │  ┌──────────────┐  ┌──────────────┐                    │  │
│  │  │ session      │  │ token-valid  │                    │  │
│  │  │ -gateway     │  │ -ation-svc   │                    │  │
│  │  │ CLAUDE.md ★  │  │ CLAUDE.md ★  │                    │  │
│  │  └──────────────┘  └──────────────┘                    │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │  FRONTEND REPOS                                          │ │
│  │                                                           │ │
│  │  ┌──────────────────────────────────────┐               │ │
│  │  │  budget-analyzer-web                  │               │ │
│  │  │  CLAUDE.md ★                          │               │ │
│  │  │  - React patterns                     │               │ │
│  │  │  - API integration through gateway    │               │ │
│  │  └──────────────────────────────────────┘               │ │
│  └──────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘

KEY:
★ = CLAUDE.md file (AI context)
─→ = References/depends on
```

## Repository Types

### Meta Repositories
- **.github**: Organization-level templates and configurations
- **architecture-conversations**: This repository - architectural discourse

### System Repositories
- **orchestration**: Deployment coordination, NGINX config, K8s manifests, system-wide docs
- **service-common**: Shared Java library with Spring Boot patterns

### Microservice Repositories
- **transaction-service**: Transaction management API
- **currency-service**: Currency and exchange rate API
- **permission-service**: Permission management API
- **token-validation-service**: JWT validation for NGINX
- **session-gateway**: BFF for browser authentication and session management

### Frontend Repositories
- **budget-analyzer-web**: React-based web application

## CLAUDE.md Distribution

Every service repository contains a CLAUDE.md file that:
- Provides AI context specific to that service
- References service-common for inherited patterns
- Uses discovery commands instead of hardcoded lists
- Maintains thin files (~150 lines) with references to detailed docs

## Total Count
- **8 repositories with CLAUDE.md files**
- **6 production microservices**
- **1 frontend application**
- **1 shared library**
- **1 orchestration coordination point**

## AI Navigation Pattern

1. Start at `orchestration/CLAUDE.md` (system overview)
2. Discover all services via Tiltfile/K8s manifests
3. Navigate to specific service CLAUDE.md for focused work
4. Load service-common context for inherited patterns
5. Just-in-time load detailed docs as needed
