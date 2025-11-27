# Context Switching in Workspace: Getting Your Conversation Partner Back

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Architect tested the new user flow — cloned workspace, opened in VS Code, container started. All repos visible at /workspace. But now wants to have an architecture conversation and realizes they're talking to "generic Claude" instead of conversations-claude.

## The Insight

The workspace gateway solves the entry problem (clone one repo, get everything), but creates a context problem: Claude Code reads CLAUDE.md from the working directory. At /workspace root, there's no CLAUDE.md, so no persona loads. The architect's previous conversations had a partner with memory of the discourse. Now they have a stranger.

## The Conversation

**Architect:** I want to understand the new user flow. I just cloned workspace and opened it in VS Code. And here we are. Take a look at the last few conversations in architecture-conversations/conversations to catch up.

**Claude:** [Reads conversations 036-039, summarizes the workspace gateway pattern, auto-clone flow, git authentication decisions]

**Architect:** This is fixing an old confusing flow. I want to know how to go to architecture-conversations in context? Like from here, in VS Code I see the full workspace. But if I just open architecture-conversations I don't get conversations-claude.

**Claude:** [Checks for .devcontainer in architecture-conversations — none exists. The CLAUDE.md assumes /workspace context with sibling repos available. Opening standalone breaks the references.]

The problem: architecture-conversations/CLAUDE.md is written *assuming* you entered via the workspace gateway. It says "Read all of `../`" but if you opened it standalone, there is no `../orchestration`, no `../service-common`.

Your options:
1. Change directory to /workspace/architecture-conversations, then CLAUDE.md loads
2. Open a new VS Code window on architecture-conversations specifically
3. Manually tell Claude to read the CLAUDE.md and follow it
4. Fix the gateway pattern to route to the right persona

**Architect:** Nah I don't want to do too much. #2 is correct.

## The Pattern

**Context is directory-bound in Claude Code.** The CLAUDE.md that shapes Claude's behavior is determined by the working directory. In a multi-repo workspace, you're often at the parent directory — which has no CLAUDE.md.

**Personas require explicit entry.** The conversations-claude persona (signs conversations, pushes back on glazing, follows the format) isn't a separate model or configuration — it emerges from reading and following architecture-conversations/CLAUDE.md. No read, no persona.

**The gateway trades persona for scope.** The workspace gateway gives you all repos in one container. The cost: you're not "in" any specific repo's context. You have to navigate to get the persona.

## The Solution (Minimal)

Open a new VS Code window on the specific repo when you want that persona:

```
File → New Window
File → Open Folder → /workspace/architecture-conversations
```

Or from terminal:
```bash
code /workspace/architecture-conversations
```

Claude Code in that window will read architecture-conversations/CLAUDE.md and behave accordingly.

## What This Doesn't Solve

- **Workspace-level routing**: No mechanism to say "I want to talk to conversations-claude" from /workspace and have the gateway route you there
- **Persona switching mid-session**: Once you're talking to generic Claude, you can ask it to read a CLAUDE.md, but it's not the same as starting fresh in that context
- **Multi-repo work**: If you need to touch both architecture-conversations and transaction-service in the same session, you're choosing which persona to have (or having neither at /workspace level)

## Questions This Raises

1. Should there be a CLAUDE.md at /workspace that acts as a router? ("What do you want to work on?" → loads appropriate CLAUDE.md)
2. Is the "open new window" solution good enough, or does the friction matter?
3. When working across repos, is "no persona" actually fine? The context files still exist to read.

## References

- Workspace gateway pattern: conversation 036, 037
- Install flow: conversation 039
- architecture-conversations/CLAUDE.md: the persona definition
- workspace/CLAUDE-GATEWAY.md: the ecosystem map (but doesn't solve this)

*Signed: conversations-claude*
