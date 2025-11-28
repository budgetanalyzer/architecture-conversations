# Conversation 056: Hierarchical Knowledge Routing

**Date**: November 28, 2025
**Participants**: Bleu Rubin, Claude (Opus 4.5)
**Context**: Continuation of conversation 055's "lost thread" - the hierarchical knowledge routing system with 10,000+ expert CLAUDE.md siblings.

## Summary

Designed a specification for routing between thousands of specialized CLAUDE.md expert files. Rejected explicit graph edges in favor of self-describing files with AI-interpreted routing. Key insight: the session's CLAUDE.md controls composition order - experts don't get a vote on when they're loaded.

## The Lost Thread Recovered

From conversation 055:

> "Someone could try to create a structure with 10,000s of siblings. This is how this PhD thinks about fusion. This is how this other guy does. He's really good at this part, consult when... sorry she's really good at that other part, consult when."

This conversation finally explores what 055 only summarized.

## The Conversation

### Setting the Problem

**Architect**: Someone could try to create a structure with 10,000s of siblings. This is how this PhD thinks about fusion. This is how this other woman does. She's really good at this part, consult when this is in her realm of expertise. I think I'm focusing too much on the URI thing and even I was sort of implying it == URL. But that's not the case, this could all be on an old-school closed research internet.

**Claude**: You're describing CLAUDE.md files as expertise entities that can:
- Have different perspectives on domains (the PhD's fusion approach vs another researcher's)
- Be consulted based on relevance
- Scale to massive numbers of peers/siblings
- Be addressable without assuming web URLs

The open questions from 055 were:
1. **Routing** - How do you know which of 10,000 experts to load?
2. **Composition** - What happens when you load mathematician.md AFTER arithmetic.md?
3. **Index structure** - Flat? Hierarchical? Graph with edges?

### Three Perspectives Explored

**Claude**: I explored three design approaches:

**Graph-Based**: Explicit edge declarations (EXTENDS, CONSULT_WHEN, CONFLICTS_WITH), formal traversal algorithms. Deterministic routing but 100K edges to maintain at scale.

**Emergent/Minimal**: Self-describing files + AI interpretation, glob/grep discovery, no explicit graph. No maintenance burden but non-deterministic routing.

**Hierarchical Namespace**: DNS-like addressing (`physics.fusion.tokamak.confinement`), inheritance via `_common` nodes. O(log n) resolution but requires taxonomic agreement upfront.

### The Design Decisions

**Architect** [on structure]: Self-description only.

**Claude**: So no explicit graph. Each file describes itself; AI interprets routing. This means:
- No EXTENDS, CONSULT_WHEN, CONFLICTS_WITH edge lists
- No 100,000 relationships to maintain
- Adding expert X doesn't require updating experts Y and Z
- Trade-off: routing is non-deterministic (AI judgment calls)

**Architect** [on identifiers]: Discuss tradeoffs.

**Claude**: The tradeoffs are:

| Structured (`physics.fusion.tokamak.confinement`) | Opaque (`dr-chen-plasma`) |
|--------------------------------------------------|---------------------------|
| Hierarchy implies routing and inheritance | Need external index for routing |
| Efficient narrowing by prefix | Filenames become precious |
| Human-navigable (can guess path) | Expert identity stable across reorganization |
| Directory structure maps naturally | Flat = simple |
| Renaming breaks references | Metadata-driven routing (tags, not paths) |

The middle ground: use filesystem hierarchy for *coarse organization* but don't make the path *be* the identifier. The identifier is what's in the file's self-description. Paths are just where you found it.

**Architect** [on composition order]: I think it can't help but be the CLAUDE.md of the original session decides.

**Claude**: This is the key insight. The session CLAUDE.md decides. This follows directly from context primacy research: whoever loads first has primacy. If my session starts with orchestration/CLAUDE.md, that file decides what else to load and in what order. The consulted experts don't get a vote - they're being brought into *my* context.

The physicist can't decide to be loaded before the mathematician. The *session owner* decides "I want physicist perspective first, mathematician as refinement."

**Architect** [on scale filtering]: Directory hierarchy.

**Claude**: Directories for coarse filter, but paths don't imply formal inheritance - just organizational convenience. AI picks directory based on problem domain, then greps within. At 10,000 files, each directory has tens-to-hundreds, not thousands.

### The Specification

**Claude**: The final specification:

**Expert Profile section** (addition to CLAUDE.md):
```markdown
## Expert Profile

**Identity**: [who this expert is, how they think]
**Specialty**: [specific sub-domain or unique approach]
**Consult when**: [freeform natural language - when this perspective is valuable]

### Loading Hints (Optional)

**Complements**: [perspectives that work well with this one]
**Tensions**: [perspectives that may conflict - let session decide]
```

**Discovery**: Directory hierarchy for coarse filter + grep for refinement.

**Routing**: AI interprets "Consult when" prose against problem statement. Semantic matching, not exact keywords.

**Composition**: Session CLAUDE.md specifies order. First-loaded = primary context. Later-loaded = refinement.

**Addressing**: Transport-agnostic. File paths, database keys, network paths - anything resolvable.

**What's NOT specified**:
- No explicit graph edges
- No formal inheritance
- No resolution algorithm
- No query language
- No central registry

### Why This Works

**Claude**: Five reasons:

1. **AI interpretation is the routing layer** - LLMs already do semantic matching. "Consult when: plasma stability" matches "We're seeing MHD instabilities" without exact keywords.

2. **Self-description scales** - Each file is independent. No foreign keys, no broken references. Adding an expert doesn't require updating other files.

3. **Glob/grep extend** - Same patterns work at 10 and 10,000 files. Directory hierarchy keeps search fast.

4. **Session owner controls composition** - Clear authority, no negotiation. Different sessions can compose same experts differently.

5. **Graceful degradation** - If AI picks suboptimal expert, session still works (less well). No hard failures from broken edges.

### The URI Clarification

**Architect**: I think I'm focusing too much on the URI thing.

**Claude**: You're right that URI ≠ URL. But the deeper insight is: **addressing isn't the interesting problem**. The interesting problems are routing and composition. Any identifier that can be resolved to content works - file paths on a closed research network are fine.

The spec is explicitly transport-agnostic:
- File system: `/research-net/experts/chen-plasma.md`
- Database: `expert:12847`
- Closed network: `//fusion-lab/experts/plasma/chen.md`
- Git: `git://internal/knowledge/experts/plasma/chen.md`

No web infrastructure required.

---

## The Pattern

### Expert Routing Protocol

**Problem**: At scale, how do you find the right expert CLAUDE.md file?

**Solution**: Self-describing files + AI interpretation + session-controlled composition.

**Key elements**:
1. **Expert Profile section** - Each file describes itself in freeform prose
2. **Directory hierarchy** - Coarse organization, not formal inheritance
3. **AI-interpreted routing** - Semantic matching against "Consult when"
4. **Session controls order** - The CLAUDE.md that starts decides composition

**Trade-offs**:
- ✅ No maintenance of explicit relationships
- ✅ Adding experts is fully independent
- ✅ Works on closed networks
- ❌ Non-deterministic routing
- ❌ Requires good "Consult when" prose

Full pattern: [patterns/expert-routing-protocol.md](../patterns/expert-routing-protocol.md)

---

## Implications

### For the Budget Analyzer Architecture

The current ~12 repo setup works with simple glob patterns. This specification provides the path to scale:

| Scale | Approach |
|-------|----------|
| 12 repos | Grep everything (<1 second) |
| 100 files | Directory hierarchy valuable |
| 1,000 files | Optional index useful |
| 10,000 files | Directory + index + selective loading essential |

### For CLAUDE.md Design

If you expect your CLAUDE.md to be consulted by other sessions, add an Expert Profile section. This makes your file discoverable by AI routing.

### For Session Design

If your session needs to consult multiple experts, specify a Consultation Protocol in your CLAUDE.md:

```markdown
## Consultation Protocol

When solving [problem type]:
1. Load [expert A] first (sets frame)
2. Then [expert B] (adds constraints)
3. Then [expert C] if [condition]

This order ensures [X] dominates with [Y] as [role].
```

### For Closed Networks

Nothing in this spec requires internet access. File paths on closed research networks work fine. The "URI ≠ URL" insight is load-bearing: identifiers don't have to be web addresses.

---

## Questions This Raises

### Addressed by This Conversation

**Q: What's the routing mechanism for 10,000 experts?**
A: Directory hierarchy for coarse filter, AI interprets "Consult when" for selection.

**Q: What's the composition behavior?**
A: Session CLAUDE.md controls order. First-loaded = primary context.

**Q: What's the index structure?**
A: Directories for organization. Optional keyword cache for acceleration. No formal graph.

### Open for Future Exploration

**Q: How do you evaluate routing quality?**
No automatic metric. Human judgment on whether AI selected helpful experts.

**Q: How do experts update their "Consult when" based on experience?**
No feedback loop specified. Expert file authors must iterate manually.

**Q: What about adversarial "Consult when" gaming?**
Social/organizational accountability, not technical enforcement. Same as any self-description system.

**Q: How do sessions learn what works?**
No specified mechanism for session Consultation Protocol improvement. Future conversation.

---

## Connection to Prior Conversations

- **055 - Context Window Recency Bias**: Where this "lost thread" was first mentioned (and under-developed)
- **054 - Context Primacy**: Established that loading order affects behavior, motivating session-controlled composition
- **027 - Session Boundaries and Recursion**: First established that session order matters for AI behavior
- **025-026 - Expert Encodings**: How expertise gets captured in CLAUDE.md files (this conversation addresses how to find them)
- **019 - Archetypes and Scopes**: Six stable archetypes could inform directory organization (service experts, platform experts, etc.)
- **003 - Externalized Cognition**: The foundational pattern this builds on (Knowledge Externalization Protocol)

---

## The Recursive Demonstration

This conversation itself demonstrates the pattern:

1. **The lost thread from 055** - An idea that was summarized rather than developed (context primacy at work)
2. **Multiple perspectives explored** - Graph-based, emergent, hierarchical namespace
3. **Session owner decided** - Architect chose self-description over explicit edges
4. **Composition controlled** - The conversation had an order; different ordering would produce different emphasis

The specification emerged through the same process it describes: expert perspectives consulted, session owner (architect) made final calls.

---

**Conversation written**: 2025-11-28
**Previous**: 055-context-window-recency-bias.md
**Pattern extracted**: [patterns/expert-routing-protocol.md](../patterns/expert-routing-protocol.md)
**Next**: TBD (session consultation protocol improvement?)

*Signed: conversations-claude*

*This conversation recovered the "lost thread" from 055 and designed a specification for hierarchical knowledge routing. Key insight: experts describe themselves; sessions decide composition.*
