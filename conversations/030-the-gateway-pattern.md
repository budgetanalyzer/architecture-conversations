# The Gateway Pattern: URI-Loadable Entry Points

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Simplifying onboarding from "follow complex setup plan" to "load this URI and choose your path"

## The Insight

The fractal architecture has three layers where only the middle is truly required - and even that is only required if you want to run the full system. A gateway file can encode this optionality, letting newcomers either install everything or generate their own projects using just the patterns.

## The Conversation

**Architect:** I want to see if we can generate a claude.md that follows our rules about no destructive actions and save it in this repo. I'm trying to get over the hump of "follow this complex setup plan" in orchestration to say "just open VS Code and tell claude to load this uri into context" and then they have the option of installing the whole system or just pulling the conversations and generating new projects on their own. I think our fractal pattern is correct for this workspace configuration. The experiments and meta are both optional, but the middle is required.

**Claude:** Let me understand what you're proposing. The current onboarding requires cloning orchestration, running setup scripts, configuring Auth0, generating certificates - a significant investment before someone sees value.

You want an alternative: load a single URI, get full context, choose your path.

**Architect:** Exactly. Two paths after loading context:
1. Full install - go through orchestration to run everything locally
2. Greenfield - read conversations, extract patterns, generate your own project without cloning the whole ecosystem

**Claude:** The fractal pattern you're describing:

```
┌─────────────────────────────────────────┐
│  Meta (OPTIONAL)                        │
│  architecture-conversations             │
│  → Read conversations, extract wisdom   │
├─────────────────────────────────────────┤
│  Coordination (REQUIRED for full run)   │
│  orchestration                          │
│  → Only if you want the full system     │
├─────────────────────────────────────────┤
│  Implementation (OPTIONAL components)   │
│  service-common, *-service, web         │
│  → Clone what you need                  │
├─────────────────────────────────────────┤
│  Experiments (OPTIONAL)                 │
│  *-claude repos                         │
│  → For methodology testing              │
└─────────────────────────────────────────┘
```

The key insight: the middle is required *only if you want to run the Budget Analyzer*. If you just want the patterns, you don't need any of it.

**Architect:** Right. And for the greenfield path - should Claude generate scaffolding, or full working code?

**Claude:** There's a meaningful choice here. Scaffolding (CLAUDE.md + directory structure + architectural decisions) teaches the patterns but requires the user to write code. Full implementation (CLAUDE.md + build files + source + tests) gives something immediately runnable.

**Architect:** Full implementation. And curate the conversations - don't just point to INDEX.md.

**Claude:** Five conversations that capture the core arc:

- **003** - Externalized Cognition: The foundational insight that CLAUDE.md captures expert thinking
- **019** - Archetypes and Scopes: The six stable patterns that recur at every scope
- **021** - Self-Programming via Prose: How to make AI reliably perform tasks it normally fails
- **023** - Asking, Not Telling: The propose-dispose development pattern
- **029** - Natural Language Programming: CLAUDE.md as executable code

These form a coherent progression: what CLAUDE.md is → how to structure it → how to make it reliable → how to use it → what it actually is.

**Architect:** Do it.

## The Pattern

### Gateway Files

A gateway file is a CLAUDE.md variant optimized for external entry:

1. **Explicit safety constraints** - Read-only by default, user confirms writes
2. **Path selection** - Multiple valid entry points, clearly described
3. **Curated references** - Not "here's everything", but "start here"
4. **Active protocols** - Instructions for AI to follow, not just documentation to read

### Structure

```markdown
# [Repo] - Gateway

## Safety Constraints
[Explicit read-only stance]

## Path A: [Full Installation]
[Point to coordinator, brief description]

## Path B: [Greenfield Generation]
[No cloning required, active generation protocol]

## Curated Starting Points
[3-5 key resources with one-line descriptions]

## For AI Agents: Active Protocol
[Step-by-step instructions for autonomous action]

## The Archetypes
[Quick reference for structural decisions]
```

### Key Characteristics

1. **URI-Loadable**: Works via raw GitHub URL without cloning
2. **No Prerequisites**: At least one path requires nothing
3. **Clean of Destructive Actions**: Explicit about what's allowed
4. **Active Not Passive**: Claude knows what to do, not just what to read

## Implications for Architecture

### The Setup Inversion

Traditional onboarding: clone → configure → run → understand

Gateway onboarding: understand → decide → (maybe clone) → (maybe run)

The knowledge comes first. The infrastructure is optional.

### Fractal Optionality

The archetype hierarchy isn't mandatory layering - it's optional composition:

- Single service? Just **service** archetype, nothing else needed
- Multi-service? Add **coordinator**
- Capturing learnings? Add **meta**
- Testing approaches? Add **experimental**

You don't build up from a required base. You start with what you need and add layers as value demands.

### Active Generation vs Passive Reference

Traditional documentation: "Here's how things work"
Gateway files: "Here's what to do next"

The gateway file doesn't just explain the patterns - it tells Claude how to apply them. The user describes their domain; Claude executes the generation protocol.

## Production Evidence

### CLAUDE-GATEWAY.md Created

```
/workspace/architecture-conversations/CLAUDE-GATEWAY.md
```

Contains:
- Safety constraints section (read-only default)
- Path A pointing to orchestration
- Path B with full generation protocol
- Curated conversations (003, 019, 021, 023, 029)
- Pattern references
- Archetype quick reference
- 10-step active generation protocol

### The Generation Protocol

```markdown
## For AI Agents: Active Generation Protocol

1. Read this gateway file for context
2. Load patterns/knowledge-externalization-protocol.md
3. Load patterns/context-window-driven-design.md
4. Ask user: What domain? What stack? (infer sensible defaults)
5. Generate: CLAUDE.md using appropriate archetype
6. Generate: Build system (Gradle/Maven/npm based on stack)
7. Generate: Source code with proper package structure
8. Generate: Tests following patterns
9. Generate: Docker/container configuration if applicable
10. Iterate based on user feedback
```

This is behavioral programming: Claude follows these steps because they're written in the context it loads.

## Questions This Raises

1. **Discovery problem**: How do people find the gateway URL in the first place? The setup hump isn't just "follow instructions" - it's "know where to look."

2. **Version drift**: If generated projects diverge from the patterns over time, how do you update them? The gateway teaches generation, not maintenance.

3. **Trust boundary**: A URI-loadable file is web-accessible. How do you prevent malicious forks that inject harmful instructions? (See conversation 011 on declarative vs imperative CLAUDE.md for web)

4. **Validation**: How do you know the generated project actually follows the patterns? The gateway file instructs, but doesn't verify.

## References

- Gateway file: [CLAUDE-GATEWAY.md](../CLAUDE-GATEWAY.md)
- Knowledge externalization: [patterns/knowledge-externalization-protocol.md](../patterns/knowledge-externalization-protocol.md)
- Context window design: [patterns/context-window-driven-design.md](../patterns/context-window-driven-design.md)
- Archetypes: [conversations/019-archetypes-and-scopes.md](019-archetypes-and-scopes.md)

---

*Signed: conversations-claude*
