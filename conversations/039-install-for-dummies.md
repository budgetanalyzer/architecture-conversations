# Install for Dummies: The Missing Entry Point

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Architect tried to test the workspace gateway from a fresh directory and got confused. The conversation revealed a gap between documented intent and actual implementation.

## The Insight

Documentation that claims infrastructure exists doesn't make it exist. The workspace repo's CLAUDE.md said ".devcontainer/ - VS Code devcontainer configuration" but the directory wasn't there. The architect experienced exactly the confusion a new user would face.

## The Conversation

**Architect:** I'm a little confused on how to proceed. Do I clone workspace from GitHub and open in VS Code? That then prompts me to download an Alpine devcontainer which isn't us. Do I point VS Code at the gateway repo? That just showed the workspace configuration with no execution steps.

**Claude:** Let me check what we actually have...

```
/workspace/orchestration/     → Has .devcontainer/ ✓
/workspace/workspace/         → Has claude-code-sandbox/ but NO .devcontainer/ ✗
```

The orchestration repo works but requires all repos pre-cloned. The workspace repo was supposed to be the single entry point but is missing the key piece.

**Architect:** What happened was I created a test-workspace directory somewhere else, cloned workspace into it, opened in VS Code, and got nothing.

**Claude:** That's exactly the failure mode. The workspace repo on GitHub has `claude-code-sandbox/` but no `.devcontainer/`. When VS Code opens a folder without devcontainer config, it falls back to something else or shows nothing useful.

## The Fix

Three files needed:

1. **`.devcontainer/devcontainer.json`** - Points to claude-code-sandbox/
2. **`entrypoint.sh` update** - Auto-clones all repos on first container start
3. **`CLAUDE.md` update** - Documents the actual workflow

The devcontainer.json:
```json
{
  "name": "Budget Analyzer",
  "dockerComposeFile": "../claude-code-sandbox/docker-compose.yml",
  "service": "claude-dev",
  "workspaceFolder": "/workspace",
  ...
}
```

The key insight in the docker-compose volumes:
```yaml
volumes:
  - ../../:/workspace:cached
```

From `workspace/claude-code-sandbox/docker-compose.yml`, `../../` is the directory *containing* the workspace repo. So:
```
/some/dir/                  ← This becomes /workspace in container
└── workspace/              ← The cloned repo lives here
    ├── .devcontainer/
    └── claude-code-sandbox/
```

The entrypoint clones missing repos:
```bash
REPOS=("orchestration" "service-common" "transaction-service" ...)
for repo in "${REPOS[@]}"; do
  if [ ! -d "/workspace/$repo" ]; then
    git clone "https://github.com/budgetanalyzer/$repo.git" "/workspace/$repo"
  fi
done
```

## The Pattern

**Documentation-Implementation Gap**: When CLAUDE.md describes what *should* exist rather than what *does* exist, the documentation becomes a lie. The architect's confusion was identical to what any new user would experience.

**Self-Testing**: The architect testing their own system as a fresh user is the most valuable QA. The failure mode only surfaced because someone actually tried the documented workflow.

**Single Entry Point**: A multi-repo architecture needs exactly one clone → open → go path. Every additional step is friction that multiplies confusion.

## Install for Dummies (The Working Version)

```bash
# 1. Clone workspace repo
git clone git@github.com:budgetanalyzer/workspace.git

# 2. Open in VS Code
code workspace

# 3. Click "Reopen in Container" when prompted

# 4. Wait (~5 min first time)
# Container builds, all repos auto-clone

# 5. Done
# All repos at /workspace/*, Claude CLI ready
```

## Implications

1. **Test your own onboarding** - Periodically try the fresh-start workflow
2. **CLAUDE.md is executable, not aspirational** - Only document what works
3. **Friction compounds** - Each unclear step multiplies the next
4. **Auto-clone is table stakes** - Multi-repo systems need automated setup

## Questions This Raises

- Should the workspace repo be the *only* place with a devcontainer? (Force single entry point)
- Should individual repos have their own devcontainers for solo development?
- How do you test devcontainer changes without pushing to GitHub first?

## References

- Implementation: `/workspace/workspace/.devcontainer/devcontainer.json`
- Entrypoint: `/workspace/workspace/claude-code-sandbox/entrypoint.sh`
- Prior conversation: 036-minimal-workspace-gateway.md, 037-implementing-the-workspace-gateway.md

*Signed: conversations-claude*
