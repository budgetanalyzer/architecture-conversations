# Stale Container Debugging

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Testing the gateway CLAUDE.md sandbox setup instructions. Devcontainer failed to start with a bind mount error for a file that didn't exist.

## The Insight

Docker containers persist configuration from their creation time. When VS Code Dev Containers finds an existing container by label, it tries to start that container — not rebuild from the current devcontainer.json. Stale containers with outdated mount configurations cause cryptic bind errors even when the current config is correct.

## The Conversation

**Architect:** ultrathink we're debugging the gateway claude file. we're doing good, it created the claude-sandbox directory, i exited vs code and opened that directory, it prompted me to re-open in devcontainer. we're doing great! now i see this error when i open the devcontainer:

```
Error response from daemon: invalid mount config for type "bind": bind source path does not exist: /home/devex/claude-sandbox/config/sandbox.conf
```

**Claude:** The error is clear — the devcontainer.json is trying to mount a config file that doesn't exist yet. Let me look at what the gateway CLAUDE.md instructions say about this setup.

*[Searched workspace for sandbox.conf references — found none]*

*[Read the actual devcontainer.json in /workspace/claude-sandbox/.devcontainer/]*

```json
{
  "name": "Claude Code Sandbox",
  "build": { "dockerfile": "Dockerfile" },
  "workspaceFolder": "/workspace",
  "workspaceMount": "source=${localWorkspaceFolder}/workspace,target=/workspace,type=bind,consistency=cached",
  "remoteUser": "vscode",
  "updateRemoteUserUID": true,
  "postCreateCommand": "echo 'Claude Code Sandbox ready. Run: claude'"
}
```

The devcontainer.json doesn't have a `sandbox.conf` mount. But Docker has a **stale container** `9fae7b73733a` from a previous setup attempt that had a different configuration with that mount.

VS Code is trying to start the old container instead of building a new one.

**The fix is simple**: Delete the old container.

**Architect:** i'm just gonna nuke all my docker containers right?

**Claude:** Yes, that works. On your host:

```bash
docker rm -f $(docker ps -aq)
```

This removes all containers (running and stopped). Then reopen the folder in VS Code and it'll build fresh.

## The Pattern

**Container State Pollution**: When iterating on devcontainer configurations, Docker preserves containers by label. The current devcontainer.json may be correct, but an old container with the same label will be reused — complete with its outdated volume mounts, environment variables, and build context.

**Diagnosis**: When a devcontainer fails with bind mount errors for paths that don't exist in your current config:
1. Check if the error references something not in your devcontainer.json
2. Look for container IDs in the error log
3. The container is stale — remove it

**Fix options**:
- Targeted: `docker rm <container-id>`
- Nuclear: `docker rm -f $(docker ps -aq)`
- VS Code: "Dev Containers: Rebuild Container" (forces fresh build)

## Implications for Architecture

This is the kind of friction that blocks scientists and domain experts from using AI sandboxes. The error message references `sandbox.conf` — a file the user never created and that doesn't appear in any current configuration. Without knowing Docker's container caching behavior, this is a dead end.

Gateway files that bootstrap sandbox environments need to account for iteration. Options:
1. **Unique container names**: Include a version or hash in the container name so old containers don't match
2. **Cleanup instructions**: Document "if things break, nuke containers" as standard recovery
3. **Rebuild-first**: Default to rebuilding rather than reusing containers

## Production Evidence

The claude-sandbox devcontainer at `/workspace/claude-sandbox/.devcontainer/`:
- `devcontainer.json`: Clean config with no sandbox.conf mount
- `Dockerfile`: Ubuntu 24.04 + Node.js + Claude Code

The error referenced container `9fae7b73733a` which had been created from a previous (likely experimental) devcontainer.json that included additional mounts.

## Questions This Raises

1. Should gateway sandbox instructions include explicit "clean slate" commands before setup?
2. Is there a way to force VS Code to always rebuild rather than reuse?
3. How do other devcontainer-based workflows handle iteration without stale state?

## References

- Implementation: `/workspace/claude-sandbox/.devcontainer/devcontainer.json`
- Pattern: Continues sandbox bootstrapping from conversation 031
- Related: Workflow coherence (034) — mixed or outdated configs cause unpredictable behavior

*Signed: conversations-claude*
