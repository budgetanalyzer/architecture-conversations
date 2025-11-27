# Implementing the Workspace Gateway

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Executing the devcontainer simplification plan from conversation 036

## The Insight

Implementation reveals constraints that planning misses. The read-only security mount that protects `claude-code-sandbox/` from AI modification also prevents AI from deleting it during cleanup — the security feature works exactly as designed, even when inconvenient.

## The Conversation

**Architect:** implement dev-container-simplification plan

**Claude:** [Reads the plan from `docs/plans/devcontainer-simplification.md`, understands the four phases, creates todo list, begins execution]

### Phase 1: Create workspace repo

Created `/workspace/workspace/` with:
- `.devcontainer/devcontainer.json` — workspaceFolder now `/workspace` (not `/workspace/orchestration`)
- `claude-code-sandbox/` — Dockerfile, docker-compose.yml, entrypoint.sh, setup-env.sh, bash_aliases.sh
- `CLAUDE.md` — minimal entry point ("This repo exists solely to provide the development environment")
- `CLAUDE-GATEWAY.md` — ecosystem navigation map
- `README.md` — human version

Key path changes in docker-compose.yml:
```yaml
volumes:
  - ../../:/workspace:cached
  - .:/workspace/workspace/claude-code-sandbox:ro  # Changed from orchestration
```

### Phase 2: Clean up orchestration

- Deleted `.devcontainer/` ✓
- Updated `CLAUDE.md` — replaced 20 lines about containerized agents with 3 lines pointing to workspace repo
- Updated `README.md` — quick start now says "use workspace repo for dev environment"
- **Could not delete** `claude-code-sandbox/` — mounted read-only, requires host access

**Architect:** [No response needed, Claude continued]

### Phase 3: Delete experimental repos

Removed:
- `claude-sandbox/`
- `orchestration-claude/`
- `service-common-claude/`
- `no-context-claude/`

### Phase 4: Test

Verified structure exists. Cannot test devcontainer from inside container — that's a bootstrap problem.

## The Pattern

**Security constraints apply bidirectionally.** The same read-only mount that prevents AI from modifying its own sandbox configuration also prevents AI from cleaning up that configuration when it's no longer needed. This isn't a bug — it's the security model working correctly.

**Implementation as discovery.** The plan said "Delete orchestration/claude-code-sandbox/" but didn't account for the fact that executing from inside the container means executing with the security constraints the container enforces. The cleanup must happen from the host.

## Implications for Architecture

1. **Plan vs Execute environments differ.** Plans written by humans (or AI in conversation mode) assume host access. Execution by AI happens inside containers with restricted access. Plans need to account for this.

2. **Handoff points emerge during implementation.** The plan didn't identify "delete claude-code-sandbox from host" as a handoff point, but implementation revealed it as one.

3. **The security model is correct.** Even when it creates friction, the read-only mount is doing exactly what it should. The friction is a feature, not a bug.

## What Was Created

```
/workspace/workspace/
├── .devcontainer/
│   └── devcontainer.json
├── claude-code-sandbox/
│   ├── Dockerfile
│   ├── docker-compose.yml
│   ├── entrypoint.sh
│   ├── setup-env.sh
│   └── bash_aliases.sh
├── CLAUDE.md
├── CLAUDE-GATEWAY.md
├── README.md
└── .gitignore
```

## Remaining Manual Step

From the host (outside container):
```bash
rm -rf /path/to/orchestration/claude-code-sandbox
```

This cannot be automated by AI — and that's the point.

## Questions This Raises

- Should plans explicitly mark "host-only" steps?
- Is there a pattern for AI to request host-side cleanup?
- Does the friction of manual cleanup serve as a useful checkpoint?

## References

- Plan: `docs/plans/devcontainer-simplification.md`
- Prior conversation: `conversations/036-minimal-workspace-gateway.md`
- New repo: `/workspace/workspace/`

*Signed: conversations-claude*
