# Devcontainer Simplification Plan

## Recommendation: Minimal Workspace Gateway Repo

Create a `workspace` repo that is intentionally minimal - just the dev environment config and AI navigation files. No content, just the front door.

```
/workspace/
├── workspace/                      # NEW - minimal gateway repo
│   ├── .devcontainer/
│   │   └── devcontainer.json
│   ├── claude-code-sandbox/        # Moved from orchestration
│   │   ├── Dockerfile
│   │   ├── docker-compose.yml
│   │   └── entrypoint.sh
│   ├── CLAUDE.md                   # "This is the dev entry point"
│   ├── CLAUDE-GATEWAY.md           # Ecosystem map for AI navigation
│   └── README.md                   # Human version
├── orchestration/                  # Pure orchestration (no devcontainer)
├── transaction-service/
├── architecture-conversations/     # Stays separate - discourse lives here
└── ...
```

## Why This Approach

1. **Minimal by design**: This repo's only job is to be the front door
2. **Pure navigation layer**: AI learns *where everything is*, not *what everything does*
3. **No content duplication**: Services own their code, architecture-conversations owns discourse
4. **Conceptually honest**: The repo you open IS about workspace entry, nothing more
5. **CLAUDE-GATEWAY.md as ecosystem map**: Single place that knows about all repos

## File Contents

### CLAUDE.md (minimal)
```markdown
# Workspace Entry Point

This repo exists solely to provide the development environment.
For ecosystem navigation, see CLAUDE-GATEWAY.md.
```

### CLAUDE-GATEWAY.md (ecosystem map)
```markdown
# Budget Analyzer Ecosystem

## Services
| Repo | Purpose |
|------|---------|
| orchestration/ | System coordination, start here for runtime |
| transaction-service/ | Transaction domain |
| currency-service/ | Currency conversion |
| permission-service/ | Authorization |
| session-gateway/ | BFF for browser security |
| token-validation-service/ | JWT validation |

## Frontend
| Repo | Purpose |
|------|---------|
| budget-analyzer-web/ | React frontend |

## Shared
| Repo | Purpose |
|------|---------|
| service-common/ | Shared Spring Boot patterns |
| checkstyle-config/ | Code style rules |

## Meta
| Repo | Purpose |
|------|---------|
| architecture-conversations/ | Architectural discourse and patterns |

## Navigation
- Runtime context: Start with orchestration/CLAUDE.md
- Design rationale: Start with architecture-conversations/
- Service implementation: Navigate to specific service
```

## Implementation Steps

### Phase 1: Create workspace repo
1. Create new GitHub repo `workspace`
2. Clone to /workspace/workspace
3. Create .devcontainer/devcontainer.json (adapt from orchestration)
4. Copy claude-code-sandbox/ from orchestration (update paths)
5. Create CLAUDE.md (minimal entry point context)
6. Create CLAUDE-GATEWAY.md (ecosystem map)
7. Create README.md (human version)
8. Update docker-compose.yml volume paths for new location

### Phase 2: Clean up orchestration
1. Delete orchestration/.devcontainer/
2. Delete orchestration/claude-code-sandbox/
3. Update orchestration/CLAUDE.md - remove devcontainer references
4. Update orchestration/README.md - point to workspace repo for dev setup

### Phase 3: Delete experimental repos
Delete these obsolete experiments:
- claude-sandbox/
- orchestration-claude/
- service-common-claude/
- no-context-claude/

### Phase 4: Test
1. Open workspace repo in VS Code
2. Verify devcontainer builds and starts
3. Verify all sibling repos accessible at /workspace/*
4. Verify Claude Code can navigate via CLAUDE-GATEWAY.md

## Critical Files

| File | Action |
|------|--------|
| workspace/.devcontainer/devcontainer.json | Create |
| workspace/claude-code-sandbox/* | Copy from orchestration, update paths |
| workspace/CLAUDE.md | Create (minimal) |
| workspace/CLAUDE-GATEWAY.md | Create (ecosystem map) |
| workspace/README.md | Create |
| orchestration/.devcontainer/* | Delete |
| orchestration/claude-code-sandbox/* | Delete |
| orchestration/CLAUDE.md | Update |
| orchestration/README.md | Update |
