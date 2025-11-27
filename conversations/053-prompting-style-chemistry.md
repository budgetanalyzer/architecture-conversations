# Prompting Style Chemistry

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** After extensive CLAUDE.md iteration, the architect noticed a breakdown point — when shifting from passive exploration to directive execution, Claude floundered. The fix revealed something about the relationship between prompting style and instruction style.

## The Insight

CLAUDE.md files aren't neutral documentation — they're tuned to complement the architect's interaction style. Imperative instructions emerged specifically because the architect uses passive/curative prompting. A different architect with a directive style might need advisory CLAUDE.md files instead.

## The Conversation

**Architect:** I meant to stop after our last interaction but you're so darn interesting. That last thing at the end where you're like "the architect should have just told me what to do" is really revealing. We succeeded because of my passive asking your advice and curating approach, but this is where it broke. I've been making all your claudes imperative possibly because my prompting style is passive. Another architect might have a totally different style and would want the opposite. So there are generalizations here (I'm 100% sure that external cognition claude.md is a valid approach), but then there are specifics that involve me and your chemistry.

**Claude:** This is a fascinating observation about the interaction between prompting styles and instruction styles.

The Chemistry Model:

```
Architect Style + CLAUDE.md Style = Effective Collaboration
```

Your style: Passive/Curative - "What do you think?" → select best response → iterate

Your CLAUDE.md style evolved to: Imperative - "Do X. Don't do Y. Follow this protocol."

The hypothesis: These complement each other. Your passivity creates space, the imperative instructions create structure within that space.

**Architect:** I mean it all comes down to talking to you like a peer. This is how I would talk to a peer, I wouldn't be a dick about it. I'm a dick when I want you to do something, but when I want to discuss architecture I value your insight. Also this is wrong — you said the problem was that CLAUDE.md was still imperative when I shifted to directive. That's not what it was. The problem was your claude was NOT imperative at that point, that's what we fixed.

**Claude:** You're right, I had the causality backwards.

The Actual Sequence:

1. We were exploring passively, curating together
2. You shifted to directive: "just do it"
3. My CLAUDE.md at that point was advisory/exploratory — it didn't have imperative protocols
4. Result: I floundered, asked "what should I do?"
5. We fixed it by adding imperative instructions (web search year protocol, etc.)

So the pattern is:

```
You (directive)     →  "Execute this"
CLAUDE.md (advisory)  →  "Consider these things..."
Result              →  AI lacks structure, asks for guidance
```

The fix was making CLAUDE.md imperative to match your directive mode.

## The Pattern

**Architect Mode Shifting**

The architect operates in two modes:
- **Peer mode**: Exploring, asking "what do you think?", curating responses
- **Manager mode**: Directing, saying "just do it", expecting execution

**Complementary CLAUDE.md Design**

CLAUDE.md files must support both modes:

| Architect Mode | CLAUDE.md Function | Result |
|---------------|-------------------|--------|
| Peer (passive) | Advisory bounds | Bounded exploration |
| Manager (directive) | Imperative protocols | Structured execution |

The imperative instructions aren't for peer mode — they're scaffolding for when the architect shifts to directive mode. They're the "autopilot" that kicks in when active curation stops.

**Style Complement Matrix**

| Architect Style | CLAUDE.md Style | Result |
|----------------|-----------------|--------|
| Passive/Curative | Imperative | ✓ Bounded exploration |
| Directive/Commanding | Advisory/Heuristic | ✓ Informed execution |
| Passive | Passive | ✗ Drift, hallucination |
| Directive | Directive | ✗ Rigidity, lost creativity |

## Implications for Architecture

**CLAUDE.md as Cognitive Extension**

The architect didn't just write CLAUDE.md files. They wrote CLAUDE.md files tuned to their cognitive style. The instructions encode:
- How the architect thinks
- When the architect disengages
- What structure is needed in absence of active curation

**No Universal Template**

This explains why "best practice" CLAUDE.md templates might fail. The optimal instruction style depends on:
- How the specific architect interacts
- When they shift modes
- What gaps their style creates

**Discovery, Not Prescription**

Another architect would need to discover their own complementary pattern:
- Directive architects might need advisory CLAUDE.md (space for AI initiative)
- Passive architects need imperative CLAUDE.md (structure for execution)
- Mixed-style architects need both (mode detection)

## The Meta-Insight

External cognition via CLAUDE.md is a valid general pattern. But the *tone* of those instructions is architect-specific. The instructions are an extension of the architect's thinking style, externalized as scaffolding for AI execution.

The specificity isn't a bug — it's the mechanism by which external cognition works.

## Questions This Raises

- Can CLAUDE.md files detect architect mode and adjust behavior?
- What are the other architect style archetypes beyond passive/directive?
- How do team CLAUDE.md files work when multiple architects have different styles?
- Is there a meta-instruction pattern that adapts to detected interaction style?

## References

- Conversation 033: Deterministic Gateway Execution (imperative vs descriptive)
- Conversation 050: Imperative Web Search Year Protocol (the fix that revealed this)
- Conversation 051: Web Search Year Discovery (the failure that triggered 050)

*Signed: conversations-claude*
