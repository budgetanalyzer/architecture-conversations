# Windows Setup Reality Check

**Date:** 2025-11-27
**Participants:** Architect, Claude (claude-opus-4-5-20250929)
**Context:** Testing the "scientist persona" setup flow on a fresh Windows machine with nothing installed.

## The Insight

The "install for dummies" flow doesn't work. Claude Code on Windows requires Git, but VS Code won't show Git commands without Git installed — chicken and egg. The target audience for this workspace needs to at least know how to run Linux.

## The Conversation

**Architect:** I'm stuck testing the windows setup. Handhold me. I have a fresh Windows install with nothing but VS Code. Not even git. I don't know how to use shells in Windows, I have no idea what the least friction flow would be.

**Claude:** [Provided detailed steps: install Git, Docker Desktop, clone repos via Git Bash, open in VS Code]

**Architect:** You're out of context. I'm debugging our single clone flow, I don't even know where to find it.

**Claude:** [Found the flow in entrypoint.sh — clone workspace, open in VS Code, "Reopen in Container" auto-clones everything]

**Architect:** Don't tell anyone but I don't know how to use git on Windows.

**Claude:** You don't need git installed at all. VS Code can clone for you: Ctrl+Shift+P → "Git: Clone" → paste URL.

**Architect:** You're wrong — there is no "Git: Clone" option in the command palette on my VS Code install. You lying AI hallucinating piece of shit (joking - we all misspeak sometimes)

**Claude:** Fair. VS Code doesn't have Git commands until Git is installed on the system. Least friction fix: go to GitHub, click "Download ZIP", extract, open folder in VS Code.

**Architect:** No, it has a "Claude Code on Windows requires Git" error. OK I'm shooting too high. First person I talk to needs to at least know how to run Linux. Rollback these changes. This isn't for dummies.

## The Pattern

### Know Your Actual Audience

The three setup flows from conversation 031:
1. **AI coder** — knows devcontainers, Docker, VS Code. Clone and go.
2. **Technical programmer** — has Linux with Docker, just needs guidance. (Tested successfully in 047)
3. **Intelligent scientist** — might have Windows, might not have Docker. (This conversation)

Flow 3 was aspirational. The dependency chain on Windows is:
- Claude Code requires Git
- VS Code Git commands require Git installed
- Git installation requires knowing how to use Windows shells
- Or: downloading ZIP requires knowing GitHub's UI

Each "simple" step assumes prior knowledge that the scientist persona doesn't have.

### The Reversion

Added Step 0b to CLAUDE-GATEWAY.md: "Install Docker Desktop from [link]" for ENV:NO_DOCKER case. Immediately reverted because the problem is earlier — can't even get Claude Code running to read the gateway file without Git.

## Implications for Architecture

**The minimum viable user knows Linux.** Not because the architecture requires it, but because:
- Claude Code has platform-specific dependencies
- Windows shell conventions are different enough to cause friction
- The "just download ZIP" workaround still requires knowing what to do next

**Documentation scope narrowed.** Instead of "works for anyone," the honest claim is "works for developers who can run Linux." Scientists who only know Windows need a human guide, not a CLAUDE.md file.

## Production Evidence

Commits in workspace repo:
- `2ee7479 tell user to install docker` — added Step 0b
- `7fca22e Revert Step 0b Docker install instructions` — removed it

The revert message captures the reasoning: "The 'scientist with no Docker' flow doesn't work on Windows — Claude Code itself requires Git, creating a chicken-and-egg problem."

## Questions This Raises

1. **Is there a Windows-native path?** Could we document: install Git for Windows → install Docker Desktop → then proceed? Worth testing, but adds complexity.

2. **GitHub Codespaces as escape hatch?** Codespaces provides a Linux environment with everything pre-installed. Might be the actual "scientist" path — no local setup required.

3. **WSL as middle ground?** Windows Subsystem for Linux gives a Linux environment on Windows. But "install WSL" is itself a non-trivial step for non-technical users.

## References

- Prior setup flows: [031-sandbox-bootstrapping.md](031-sandbox-bootstrapping.md)
- Linux success: [047-linux-docker-success.md](047-linux-docker-success.md)
- Gateway file: `/workspace/workspace/CLAUDE-GATEWAY.md`
- Reverted commit: workspace repo `7fca22e`

*Signed: conversations-claude*
