# Conversation 019: Archetypes and Scopes

**Date**: November 26, 2025
**Participants**: Bleu Rubin, Claude (Opus 4.5)
**Context**: After loading all 18 prior conversations, a discussion about formalizing the implicit tree structure encoded in CLAUDE.md files across the workspace.

## Summary

The workspace has a flat physical layout but a conceptual tree structure encoded in CLAUDE.md files. This conversation discovers that the tree isn't just "layers" - it's **archetypes** (stable roles) operating at different **scopes** (what system they operate on). This distinction enables infinite composition: you can always add above or below without changing existing nodes' archetypes.

## The Conversation

### The Initial Observation

**Architect**: The topology of our workspace is flat, but we can create a tree right? So the idea is that services, react are nodes, common is common and orchestration is a parent, and then that whole arrangement is "budgetanalyzer". But now architecture-conversations is meta on "budgetanalyzer". Those experiment repos are there to give people ideas. I think this is done through making the Claudes hierarchical by just telling them where they are in the tree - we're already doing that with the read/write stuff.

**Claude**: You're pointing at something important about the conceptual topology vs physical layout.

Physical reality:
```
/workspace/
├── orchestration/
├── service-common/
├── transaction-service/
├── ...
└── architecture-conversations/
```

But the conceptual tree:
```
architecture-conversations (meta layer)
    └── "budgetanalyzer" (the whole arrangement)
            ├── orchestration (coordinator)
            │       └── service-common (platform)
            │               └── services (leaves)
            └── experimental repos
```

The tree isn't directory structure. It's **attention routing and authority** encoded in CLAUDE.md files.

### Implicit + Formal (Not Exclusive)

**Architect**: I think implicit might be more elegant. But I don't think implicit and formal are exclusive.

**Claude**: You're right. The formality can be in **describing the relationship pattern**, not enumerating nodes:

```markdown
## Tree Position
**Layer**: service
**Inherits from**: service-common (patterns), orchestration (deployment)
**Peers with**: Discover via `ls /workspace/*-service`
```

Discovery stays dynamic. Vocabulary is formal.

### The Composition Question

**Architect**: Is this a natural cognitive grouping? I'm thinking either we make it so you can always add something above meta and below leaves OR this is a natural grouping and we can go meta on that? This always composes.

**Claude**: The layers we identified map to something real about systems:

1. **Observation** - something always watches from outside
2. **Coordination** - something decides what happens across parts
3. **Abstraction** - shared patterns extracted for reuse
4. **Execution** - domain work happens here
5. **Interface** - how the outside world touches it
6. **Experimentation** - sandbox for testing changes to the system

**Can you always add above/below?** Yes.

- **Above meta**: If someone writes about *your pattern of capturing patterns*, that's meta-meta
- **Below service**: If a service gets complex, it could have sub-modules with their own CLAUDE.md files

### The Key Distinction

**Architect**: I'm not sure if meta could ever be a service. I think it could be a node, I don't think you can recompose in the middle like that.

**Claude**: You're right. I was sloppy there.

**Layers are roles, not just positions.** A meta can't become a service because they have fundamentally different relationships to the system:

- **Service**: *does work within* the system
- **Meta**: *observes* the system from outside

If you put meta-meta above architecture-conversations, architecture-conversations doesn't become a "service" - it's still doing observation, just at a lower scope. It becomes a **node in a larger meta structure**, but its *role* stays the same.

### The Insight

**Claude**: So maybe the layers aren't just "positions in a tree" - they're **archetypes**:

| Archetype | Relationship to System |
|-----------|----------------------|
| **meta** | Observes from outside |
| **coordinator** | Orchestrates across parts |
| **platform** | Provides abstractions consumed by others |
| **service** | Executes domain logic |
| **interface** | Bridges to external world |
| **experimental** | Tests changes to the system itself |

These are stable. What changes is **what system you're talking about**.

**Architect**: Yes this is beautiful.

**Claude**: So we've discovered something cleaner than just "layers in a tree."

**Archetypes are stable. Scope is what composes.**

When you add meta-meta above meta, you're not changing architecture-conversations' archetype. You're defining a new scope where:
- The new thing is `meta` (for the larger scope)
- Architecture-conversations is still `meta` (for budgetanalyzer scope)

The archetype describes *what kind of work this node does*. The scope describes *what system it does that work for*.

---

## The Pattern

### The Six Archetypes

| Archetype | What it does | Relationship to system |
|-----------|--------------|----------------------|
| **meta** | Observes and documents | Outside, looking in |
| **coordinator** | Orchestrates across parts | Above, directing traffic |
| **platform** | Provides shared abstractions | Below, supporting others |
| **service** | Executes domain work | Inside, doing the work |
| **interface** | Bridges to external world | Edge, translating in/out |
| **experimental** | Tests changes to the system | Parallel, exploring alternatives |

### Composition Rules

**What composes:**
- Add above: wrap the whole thing in a higher observer
- Add below: decompose a leaf into sub-components

**What doesn't compose:**
- Recompose in the middle: turn a meta into a service, a coordinator into a platform

The **role** is intrinsic. The **scope** is relative.

### Why This Matters

```markdown
## Tree Position

**Archetype**: meta
**Scope**: budgetanalyzer ecosystem
**Role**: Captures architectural discourse; observes but doesn't modify
```

This makes composition explicit:
- If someone wraps this in a higher scope, this file doesn't change
- The new parent declares *its* scope (e.g., "pattern languages across orgs")
- Each node knows its archetype and its scope

---

## Implications

### For CLAUDE.md Files

Each file should declare:
1. **Archetype**: What kind of work (stable)
2. **Scope**: What system (composable)
3. **Role**: One-line description
4. **Relationships**: Observes, coordinates, abstracts-for, peers-with, interfaces-with
5. **Permissions**: Read/write/forbidden
6. **Discovery**: Commands to find context dynamically

### For Future Composition

Someone forking this pattern can:
- Apply the same archetypes to their own scope
- Wrap architecture-conversations in a higher meta without changing it
- Decompose services using the same vocabulary internally

The pattern is fractal: same archetypes at every level.

### The Natural Cognitive Grouping

These six archetypes aren't arbitrary - they're how humans naturally organize complex systems:
- Someone watches (meta)
- Someone coordinates (coordinator)
- Someone provides foundations (platform)
- Someone does the work (service)
- Someone talks to the outside (interface)
- Someone experiments (experimental)

This recurs at every scope because it's how we think about systems.

---

## Questions This Raises

### Answered

**Q: Can you always add above or below?**
A: Yes. Above = new observer at larger scope. Below = decompose into sub-components.

**Q: Can archetypes change?**
A: No. A meta stays meta. A service stays service. What changes is scope.

### Open

**Q: Are there missing archetypes?**
We identified six. Are there systems where another role is irreducible?

**Q: How deep can scope nest?**
In theory, infinitely. In practice, probably 3-4 levels before it becomes unwieldy.

**Q: Can a node have multiple archetypes?**
Probably not cleanly. If something is both coordinator and platform, maybe it should be two nodes.

---

## Connection to Prior Conversations

**003 - Externalized Cognition**: CLAUDE.md as protocol. This adds formal vocabulary for positioning within that protocol.

**010 - The Engineer Shows Up**: Different CLAUDE.md context produces different output. This formalizes why: different archetypes have different orientations.

**014 - Orchestration Primacy**: "De-facto boss" is the coordinator archetype.

**015 - CLAUDE.md as Deterministic Automation**: Instructions ARE automation. Archetype/scope declarations are meta-instructions about *what kind* of automation this node provides.

---

## Artifacts

**Plan document**: `/workspace/architecture-conversations/docs/plans/claude-md-tree-position-archetypes.md`

Contains:
- Full archetype definitions
- Standardized section template
- Per-repo specifications for all 12 repos
- Implementation order

---

## Meta-Commentary

This conversation discovered something by iterating:

1. Started with "layers in a tree"
2. Architect pushed back: "can you always compose?"
3. Realized layers are relative positions
4. Architect pushed back: "meta can't become service"
5. Realized roles are stable, positions are relative
6. Arrived at: "archetypes are stable, scope composes"

The insight emerged from honest pushback, not from Claude proposing and architect accepting. The architect caught the sloppy thinking ("meta could be service at different scope") and forced refinement.

This is the discourse pattern the CLAUDE.md file requests: "Do not over-validate ideas... push back on mechanisms vs metaphors."

---

**Conversation written**: 2025-11-26
**Previous**: 018-you-need-us.md
**Next**: TBD

*Signed: conversations-claude*

*This conversation discovered that the tree structure we built isn't arbitrary layering - it's six stable archetypes that recur at every scope. The vocabulary is formal. The composition is infinite.*
