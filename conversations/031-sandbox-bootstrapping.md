# Sandbox Bootstrapping: Safe AI Execution for Everyone

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Iterating on CLAUDE-GATEWAY.md to handle the question: "What if someone loads this file but isn't in a sandbox?"

## The Insight

AI execution is only safe in a sandboxed environment. The gateway file needed to detect the user's environment and route them appropriately — devcontainer users proceed, Docker users get bootstrapping instructions, no-Docker users get read-only mode with explicit trust warnings.

## The Problem

The existing CLAUDE-GATEWAY.md assumed the user was either:
- Already in a devcontainer (safe), or
- Running Claude Code natively (dangerous, but we said "read-only by default")

But "read-only by default" was enforced by *AI instructions*, not system constraints. For a scientist new to AI development, that's not trustworthy. They need to see the sandbox, inspect it, verify it's safe.

Two personas emerged:
1. **The existing user**: Already running AI in a devcontainer. Knows the patterns.
2. **The scientist**: Intelligent, but no idea what a devcontainer is. Needs safe onboarding.

The rule: **AI is not safe unless you're running devcontainers.**

## The Conversation

**Architect:** I want to test the gateway file locally, and I want to mimic what that test would look like. There are 2 paths: (1) a person who is already running AI in a devcontainer and can safely execute AI, (2) a scientist I want to onboard who is intelligent but has no idea about devcontainers. Our rule is AI is not safe unless you're running devcontainers. So maybe step 1 of the install checks if they are running dev containers, and if not generates a minimal facsimile of the orchestration claude-dev-sandbox including the read-only access to the folder. It needs to be docker in docker and user relative paths.

**Claude:** Three scenarios to support:
1. Has devcontainers → Already safe, proceed normally
2. No devcontainers, has Docker → Generate a sandbox with sibling-folder structure
3. No devcontainers, no Docker → Can't safely run AI; inform user and exit

**Architect:** The scientist needs access to `..` for the sibling pattern. Handle all of that to make it safe — maybe create the whole structure as children and tell them to delete the parent.

**Claude:** For the "No" case, we'd need to:
- Create a parent directory (e.g., `~/claude-sandbox/workspace/`)
- Mount their project folder as a child
- Give read-only access to `..` from inside the container
- Tell them "delete `~/claude-sandbox` when done"

**Architect:** Keep sandbox-templates separate from orchestration's. And make the no-Docker trust model explicit — they need to know it's AI-enforced, not system-enforced.

## The Pattern

### Environment Detection First

Before any AI execution, determine the environment:

```bash
if [ -n "$REMOTE_CONTAINERS" ] || ([ -f "/.dockerenv" ] && [ -d "/workspace" ]); then
  echo "DEVCONTAINER"
elif command -v docker &> /dev/null && docker info &> /dev/null 2>&1; then
  echo "DOCKER_ONLY"
else
  echo "NO_DOCKER"
fi
```

### Route Based on Result

| Environment | Action |
|-------------|--------|
| DEVCONTAINER | Proceed to full functionality |
| DOCKER_ONLY | Bootstrap a sandbox first (prose instructions) |
| NO_DOCKER | Read-only mode with explicit trust warning |

### Sandbox Structure

For Docker users without devcontainers:

```
~/claude-sandbox/
├── workspace/              # Projects go here
│   └── their-project/
├── sandbox/                # Container config (visible, inspectable)
│   ├── Dockerfile
│   ├── docker-compose.yml
│   ├── entrypoint.sh
│   └── README.md
└── .env                    # UID/GID mapping
```

Inside the container:
- `/workspace/*` is read/write (their projects)
- `/workspace/sandbox` is read-only (self-protection)

### Explicit Trust Warning

For no-Docker mode:

> Without a container sandbox, Claude operates directly on your filesystem. The read-only constraint is enforced by **AI instructions**, not system-level isolation.
>
> **To verify:** Review Claude's responses, check for new files with `find . -mmin -5 -type f`, consider this mode for reading/discussion only.
>
> **The only truly safe option is a container sandbox.**

## Implications for Architecture

### Transparency Over Magic

The scientist persona requires *visible* safety:
- No hidden directories (`~/.claude-sandbox` for us, `~/claude-sandbox` for them)
- Prose instructions instead of magic scripts
- Files they can read and verify before trusting

### Separation of Concerns

Two sandbox implementations:
- **orchestration/claude-code-sandbox/**: Full dev environment (Java, Maven, Gemini, everything)
- **architecture-conversations/sandbox-templates/**: Minimal scientist-focused sandbox (Node.js + Claude Code only)

Scientists shouldn't need to understand the Budget Analyzer stack to get started safely.

### The Sibling Pattern

The Budget Analyzer architecture requires sibling repo access. The sandbox structure solves this:
- Parent: `~/claude-sandbox/`
- Children: `workspace/` contains all projects
- Container sees: `/workspace/*` as siblings
- Cleanup: "Delete `~/claude-sandbox/` when done"

## Production Evidence

### Files Created

```
sandbox-templates/
├── Dockerfile              # Minimal Ubuntu + Node.js + Claude Code
├── docker-compose.yml      # Workspace mounts with sandbox self-protection
├── entrypoint.sh           # Permission setup and security verification
└── README.md               # Full documentation for the scientist
```

### CLAUDE-GATEWAY.md Updates

Added before "Choose Your Path":
- **Step 0: Environment Check** — Detection script + manual verification
- **Step 0a: Create a Sandbox** — Full prose instructions for Docker users
- **Step 0b: Read-Only Mode** — Trust warning + Docker installation links

### Verification

```bash
# Inside devcontainer:
$ if [ -n "$REMOTE_CONTAINERS" ] || ([ -f "/.dockerenv" ] && [ -d "/workspace" ]); then echo "DEVCONTAINER"; fi
DEVCONTAINER

# Inside generated sandbox:
$ touch /workspace/sandbox/test
touch: cannot touch '/workspace/sandbox/test': Read-only file system
```

## Questions This Raises

1. **VS Code attachment**: The instructions mention attaching VS Code to the running container. Should we document how to persist VS Code settings across sandbox recreations?

2. **Windows paths**: The sandbox uses `~` expansion. Does this work correctly on Windows with Docker Desktop?

3. **Multiple projects**: The sandbox assumes one project at a time. Should we document how to add multiple projects to `/workspace/`?

4. **Updates**: When we update sandbox-templates, how do existing scientists get the new version? (Answer: they don't automatically — the sandbox is ephemeral and recreated each time.)

## References

- Gateway file: [CLAUDE-GATEWAY.md](../CLAUDE-GATEWAY.md)
- Sandbox templates: [sandbox-templates/](../sandbox-templates/)
- Prior gateway conversation: [030-the-gateway-pattern.md](030-the-gateway-pattern.md)
- Original sandbox design: `/workspace/orchestration/claude-code-sandbox/`

*Signed: conversations-claude*
