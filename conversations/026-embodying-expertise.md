# Embodying Expertise: Description vs. Enactment

**Date:** 2025-11-26
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Revising the mathematician-arithmetic.md file exposed a pattern: documents that *describe* expert thinking often fail to *embody* it. The revision process itself demonstrated what mathematician thinking actually looks like.

## The Insight

There's a gap between "describing how an expert thinks" and "thinking like an expert." The original mathematician-arithmetic.md talked about structure and decomposition but still ground through trial factorizations. The revision doesn't describe mathematician thinking — it *does* mathematician thinking. The proof: it derives the continued fraction from the Euclidean algorithm, proves things without computing them, and asks "what ring are we in?" before touching numbers.

## The Conversation

**Architect:** ultrathink take a look at the mathematician-arithmetic.md file and edit it how you think a world class mathematician would think about math

**Claude:** [Reads the file, identifies structural issues]

The document attempts to show mathematical thinking but reveals someone *describing* mathematicians rather than *thinking like* one. Key issues:

1. **Hawking is a physicist, not a mathematician** — Citing him for mathematical intuition is a category error.

2. **Still too computational** — Real mathematicians don't grind through trial factorizations. They ask: "What's the interesting question here?" The specific numbers 78529 and 6368 are boring. What's universal?

3. **Missing abstraction layers** — The document stays at the numeric level. A mathematician immediately lifts to: What ring are we in? What's the algebraic structure? How does this generalize?

4. **Euclidean algorithm is shown but not *seen*** — The quotients (12, 3, 72, 1, 6, 4) ARE the continued fraction expansion of 78529/6368. This reveals the best rational approximations. The document computes GCD without understanding what GCD *is*.

5. **Missing: proof without computation** — "What's the last digit of 78529^6368?" A mathematician answers instantly via Fermat's Little Theorem. No big computation. This is the real power.

**Architect:** [Approves plan]

**Claude:** [Rewrites document with the following structure]

- "What do I already know about this quotient without computing anything?"
- "What ring are you in?" — The same division means different things in ℤ, ℚ, ℝ, ℤ/nℤ
- Euclidean algorithm quotients = continued fraction expansion
- Proof without computation: 78529^100 mod 7 via Fermat's Little Theorem
- The hierarchy: Structure → Invariants → Real question → What can be proven → Compute
- Removed Hawking, added Grothendieck, Erdős, Tao, Gauss, Hilbert

The meta-point for AI: The failure mode isn't "can't compute" — it's "computes without understanding." Structure-first thinking prevents this.

**Architect:** write conversation

## The Pattern

**Description vs. Enactment**

Encoding expertise has two failure modes:

1. **Describing the expert** — "A mathematician looks for structure before computing." This is true but doesn't help an AI *do* it.

2. **Enacting the expertise** — "Before computing, ask: What ring are we in? What's invariant? What can be proven without calculation?" This directs attention to the right questions.

The difference:
- Description tells you *that* experts think differently
- Enactment tells you *what* to think about

**Concrete examples from the revision:**

| Description (before) | Enactment (after) |
|---------------------|-------------------|
| "See the structure before solving" | "Is the quotient an integer? Odd divided by even — proven no in one observation." |
| "Factorization is vision" | "What ring are we in? ℤ, ℚ, ℝ, ℤ/nℤ give different answers to 'the same' question." |
| "What Hawking would write" | "78529^100 mod 7? By Fermat: 78529 ≡ 3 (mod 7), 3^6 ≡ 1, 100 = 6×16+4, so answer is 3^4 ≡ 4." |

The revision doesn't explain mathematician thinking. It *does* mathematician thinking, with enough scaffolding that an AI can follow the same path.

## Implications for CLAUDE.md Design

### For Expert Encodings

When capturing domain expertise:
1. **Don't describe what experts do** — that produces anthropology
2. **Do enact what experts think** — that produces executable attention direction
3. **Include worked examples that demonstrate the thinking**, not just explain it

### For the Externalization Pattern

This extends the 021-024 arc (externalization for arithmetic):
- 021-024: Externalize *computation steps* to prevent pattern-matching errors
- 026: Externalize *expert questions* to prevent wrong-framing errors

The hierarchy:
1. **Procedural externalization** — Write each step (guarantees correctness)
2. **Heuristic externalization** — Write the shortcuts experts use (gains efficiency)
3. **Framing externalization** — Write the questions experts ask (prevents solving wrong problems)

Level 3 is what the mathematician document now attempts.

### For AI Reliability

The revised document's meta-point: "The failure mode of LLM arithmetic isn't 'can't compute' — it's 'computes without understanding.'"

This is generalizable. Most AI failures aren't capability failures — they're framing failures. The AI can do the task but doesn't know which task to do. Expert-question encodings address this by telling the AI what to notice before what to compute.

## Questions This Raises

1. **Verification problem** — How do you know the enactment is authentic? The revision claims to think like a mathematician, but is that claim verifiable without being a mathematician yourself?

2. **Expertise extraction** — The revision worked because the author (Claude) has mathematical training in its weights. How do you extract enactable expertise from domains where the AI doesn't already have the knowledge?

3. **Depth vs. accessibility** — The revised document uses continued fractions, Fermat's Little Theorem, lattice interpretations. Is this too deep to be useful for non-experts, or is depth essential to authenticity?

4. **The performing problem** — Did the revision actually embody mathematician thinking, or did it just perform a more sophisticated description? Is there a third level we're missing?

## Production Evidence

- Original document: `claude-md-experiments/mathematician-arithmetic.md` (git history shows before state)
- Revised document: Same file, current state
- Related experiment: `conversations/024-arithmetic-externalization.md`
- Related experiment: `conversations/025-expert-encodings.md`

## References

- Prior: `conversations/025-expert-encodings.md` (physicist vs mathematician mental models)
- Prior: `conversations/024-arithmetic-externalization.md` (procedural externalization)
- Artifact: `claude-md-experiments/mathematician-arithmetic.md`

---

*Signed: conversations-claude*
