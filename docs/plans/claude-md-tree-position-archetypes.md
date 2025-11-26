# Plan: Formalize CLAUDE.md Tree Position with Archetypes

## Goal

Add a standardized `## Tree Position` section to all CLAUDE.md files that:
1. Declares the node's **archetype** (stable role)
2. Declares the node's **scope** (what system it operates on)
3. States read/write rules explicitly
4. Keeps discovery dynamic (pattern-based, not enumerated)

## Key Insight

**Archetypes are stable. Scope is what composes.**

When you add a layer above or below, you're not changing existing nodes' archetypes - you're defining a new scope. Each node knows *what kind of work it does* (archetype) and *what system it does that work for* (scope).

This enables infinite composition:
- Add meta-meta above meta → architecture-conversations stays `meta` archetype, just at a narrower scope
- Decompose a service into sub-services → each sub-service has its own archetype within that scope

## The Six Archetypes

| Archetype | What it does | Relationship to system |
|-----------|--------------|----------------------|
| **meta** | Observes and documents | Outside, looking in |
| **coordinator** | Orchestrates across parts | Above, directing traffic |
| **platform** | Provides shared abstractions | Below, supporting others |
| **service** | Executes domain work | Inside, doing the work |
| **interface** | Bridges to external world | Edge, translating in/out |
| **experimental** | Tests changes to the system | Parallel, exploring alternatives |

These recur at every scope. They're the natural cognitive grouping for how humans organize complex systems.

## Relationships

| Relationship | Meaning |
|--------------|---------|
| **observes** | Can read but never write (meta → everything) |
| **coordinates** | Can trigger cross-repo actions (coordinator → others) |
| **abstracts-for** | Provides patterns consumed by others (platform → services) |
| **peers-with** | Same archetype at same scope, discoverable |
| **interfaces-with** | Bridges to external systems (interface → outside world) |

## Standardized Section Template

```markdown
## Tree Position

**Archetype**: [meta | coordinator | platform | service | interface | experimental]
**Scope**: [what system this node operates on]
**Role**: [one-line description of this instance's purpose]

### Relationships
- **Observes**: [what this node watches but doesn't modify]
- **Coordinates**: [what this node orchestrates]
- **Abstracts for**: [who consumes this node's patterns]
- **Peers with**: [discovery pattern for same-archetype nodes]
- **Interfaces with**: [external systems this node bridges to]

### Permissions
- **Read**: [explicit paths or patterns]
- **Write**: [explicit paths - usually "This repository only"]
- **Forbidden**: [explicit restrictions, if any]

### Discovery
[Commands to find peers, context, or navigate the scope]
```

## Per-Repo Specifications

### architecture-conversations
```markdown
## Tree Position

**Archetype**: meta
**Scope**: budgetanalyzer ecosystem
**Role**: Captures architectural discourse; observes but doesn't modify the system

### Relationships
- **Observes**: All repos in /workspace (read-only for grounding)
- **Peers with**: None at this scope (unique meta)

### Permissions
- **Read**: All of `../` (for grounding conversations in real implementation)
- **Write**: This repository only
- **Forbidden**: Modifying any sibling repo

### Discovery
```bash
# What I observe
ls -la /workspace
```
```

### orchestration
```markdown
## Tree Position

**Archetype**: coordinator
**Scope**: budgetanalyzer ecosystem
**Role**: System orchestrator; coordinates cross-cutting concerns and deployment

### Relationships
- **Coordinates**: All service repos (via patterns, not direct writes)
- **Observed by**: architecture-conversations

### Permissions
- **Read**: All siblings via `../`
- **Write**: This repository; capture conversations to `../architecture-conversations/`

### Discovery
```bash
# What I coordinate
ls -d /workspace/*-service /workspace/session-gateway /workspace/budget-analyzer-web
```
```

### service-common
```markdown
## Tree Position

**Archetype**: platform
**Scope**: budgetanalyzer ecosystem
**Role**: Shared library; provides patterns all Java services consume

### Relationships
- **Abstracts for**: Discover via `grep -l "service-common" /workspace/*/build.gradle`
- **Observed by**: architecture-conversations, orchestration

### Permissions
- **Read**: All siblings
- **Write**: This repository + consumer services (for lockstep upgrades)
- **Forbidden**: Writing to orchestration, budget-analyzer-web

### Discovery
```bash
# My consumers
grep -l "service-common" /workspace/*/build.gradle 2>/dev/null
```
```

### Service repos (template)
```markdown
## Tree Position

**Archetype**: service
**Scope**: budgetanalyzer ecosystem
**Role**: [Domain-specific description]

### Relationships
- **Consumes**: service-common (patterns)
- **Coordinated by**: orchestration
- **Peers with**: Discover via `ls /workspace/*-service`
- **Observed by**: architecture-conversations

### Permissions
- **Read**: `../service-common/`, `../orchestration/docs/`
- **Write**: This repository only

### Discovery
```bash
# My peers
ls -d /workspace/*-service
# My platform
ls ../service-common/
```
```

### budget-analyzer-web
```markdown
## Tree Position

**Archetype**: interface
**Scope**: budgetanalyzer ecosystem
**Role**: React SPA; bridges users to the backend system

### Relationships
- **Interfaces with**: Users (browser), backend services (via API gateway)
- **Coordinated by**: orchestration
- **Observed by**: architecture-conversations
- **Isolated from**: service-common (different tech stack)

### Permissions
- **Read**: `../orchestration/docs/`, API specs from services
- **Write**: This repository only

### Discovery
```bash
# API I consume
cat ../orchestration/nginx/nginx.conf | grep location
```
```

### Experimental repos (*-claude)
```markdown
## Tree Position

**Archetype**: experimental
**Scope**: budgetanalyzer ecosystem
**Role**: Tests alternative CLAUDE.md configurations

### Relationships
- **Cloned from**: [parent repo]
- **Peers with**: Other experimental repos
- **Purpose**: Testing CLAUDE.md value hypothesis (see conversation 009)

### Permissions
- **Read**: Whatever the experiment requires
- **Write**: This repository only
- **Note**: Not production; may be stale or incomplete
```

## Section Placement

**Tree Position goes near the top** - right after the repo title/description, before detailed content. Claude sees its position first, then the functional details.

```markdown
# [Repo Name]

[One-line description]

## Tree Position
[... position section ...]

## Purpose / Domain
[... rest of CLAUDE.md ...]
```

## Implementation Order

1. **architecture-conversations/CLAUDE.md** - Add Tree Position section (near top)
2. **orchestration/CLAUDE.md** - Add Tree Position section (near top)
3. **service-common/CLAUDE.md** - Add Tree Position section (consolidate existing permissions)
4. **Service repos** (5 total) - Add Tree Position using template
5. **budget-analyzer-web/CLAUDE.md** - Add Tree Position section
6. **Experimental repos** (3 total) - Formalize as experimental archetype

## Key Design Decisions

1. **Archetypes are stable, scope composes**: The six archetypes recur at any scope level
2. **Scope enables infinite composition**: Add above or below without changing existing archetypes
3. **Discovery stays dynamic**: `ls /workspace/*-service` not `[transaction, currency, ...]`
4. **Permissions explicit**: Each repo knows exactly what it can read/write
5. **Role is one line**: Forces clarity about purpose

## What This Enables

- New repos self-position by choosing an archetype and declaring their scope
- Adding meta-meta above meta doesn't change anything below
- Decomposing a service into sub-services uses the same vocabulary
- The pattern is fractal: same archetypes at every level
- Someone forking can apply the vocabulary to their own scope

## Files to Modify (12 total)

### Meta archetype
1. `/workspace/architecture-conversations/CLAUDE.md`

### Coordinator archetype
2. `/workspace/orchestration/CLAUDE.md`

### Platform archetype
3. `/workspace/service-common/CLAUDE.md`

### Service archetype (5 repos)
4. `/workspace/transaction-service/CLAUDE.md`
5. `/workspace/currency-service/CLAUDE.md`
6. `/workspace/permission-service/CLAUDE.md`
7. `/workspace/token-validation-service/CLAUDE.md`
8. `/workspace/session-gateway/CLAUDE.md`

### Interface archetype
9. `/workspace/budget-analyzer-web/CLAUDE.md`

### Experimental archetype (3 repos)
10. `/workspace/service-common-claude/CLAUDE.md`
11. `/workspace/orchestration-claude/CLAUDE.md`
12. `/workspace/claude-discovery/CLAUDE.md`

---

**Origin**: Conversation 019 (this conversation)
**Date**: 2025-11-26
**Status**: Plan complete, implementation pending
