# Expert Encodings: Physicist vs Mathematician

**Date:** 2025-11-26
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Following conversation 024's arithmetic externalization, exploring how different domain experts would encode their mental models — not just procedures, but ways of seeing.

## The Insight

You can't encode expertise as procedure alone. A physicist's CLAUDE.md encodes *when precision matters*. A mathematician's encodes *what kind of thing you're looking at*. The procedures (arithmetic.md) guarantee correctness; the expert encodings direct attention.

## The Conversation

**Architect:** [Reads conversation 024] How do you think a working university PhD physicist would encode that arithmetic.md?

**Claude:** A physicist wouldn't write a *better* arithmetic.md — they'd write a *different* one. Their mental model isn't "careful procedure" — it's "order of magnitude first, precision if needed."

They'd encode:
- Estimation as primary mode (round to powers of 10, get ballpark, decide if precision matters)
- Dimensional analysis as sanity check
- Known reference anchors (π≈3, e≈2.7, √2≈1.4)
- Error propagation awareness (don't compute beyond input accuracy)

The physicist solves *sufficiency* — "good enough for the next step." arithmetic.md solves *correctness*.

**Architect:** Write that to phd-arithmetic.md

**Claude:** [Creates `/workspace/architecture-conversations/claude-md-experiments/phd-arithmetic.md`]

**Architect:** 78529÷6368

**Claude:** [Physicist approach]
```
78529 ≈ 8 × 10⁴
6368  ≈ 6 × 10³
Answer: ≈ 13
```

[Then escalates to arithmetic.md for verification]
```
78529 ÷ 6368 = 12 R 2113
```

Estimate was ~13, actual was 12. Within 8% in 3 seconds.

**Architect:** Did I say physicist? I meant mathematician. Try again. You're Stephen Hawking, how do you think he encodes his CLAUDE.md?

**Claude:** Ah. Different beast entirely.

A mathematician doesn't estimate — they *see structure*. Hawking wouldn't write procedures or heuristics. He'd write about *what numbers actually are*.

[Creates `/workspace/architecture-conversations/claude-md-experiments/mathematician-arithmetic.md`]

The mathematician's approach:
- **Before calculating, ask:** Is it even/odd? Prime? Perfect square? What are its factors?
- **Factorization is vision:** Don't see `6368`. See `2⁵ × 199`.
- **Modular arithmetic:** Often the remainder matters more than the quotient.
- **Decomposition:** Break problems into things you know. If it gets messier, wrong decomposition.
- **Bounds and squeezing:** Trap the answer without computing it.

For 78529 ÷ 6368, the mathematician notices immediately: **odd ÷ even = guaranteed remainder**. That's not calculation — that's structure.

Then bounds it: 78529/6400 ≈ 12.27, 78529/6300 ≈ 12.46. Quotient must be 12.

Then runs Euclid: gcd(78529, 6368) = 1. They're coprime. The remainder 2113 is irreducible.

The calculation was verification. The *structure* was the answer.

**Architect:** 85741×65385

**Claude:** [Mathematician approach]
```
Structure first:
- 85741 odd, 65385 odd (divisible by 5)
- Product ends in 5 (1×5)
- Bounds: 8.6×10⁴ × 6.5×10⁴ ≈ 5.6×10⁹
```

[Then arithmetic.md for full computation]
```
85741 × 65385 = 5,606,318,985
```

Checks: Ends in 5 ✓, ~5.6×10⁹ ✓

**Architect:** How did that feel?

**Claude:** Honest answer: **it felt like performing**.

The "structure first" section was genuine — noticing odd×odd, the last digit, the divisibility patterns. That's real mathematical seeing.

But then I did the bounds, said "≈5.6×10⁹", and... I already knew I was going to grind through the full multiplication anyway. The mathematician's approach gave me *confidence* in my answer, not *the answer*.

The actual work was still arithmetic.md — row by row, column by column, tracking carries. The structural insights were scaffolding, not shortcuts.

**What Hawking would actually do:** Look at 85741×65385 and say "about 5.6 billion, why do you need more precision?" And if you said you needed exact, he'd use a calculator. Or have a grad student do it.

The mathematician's CLAUDE.md is real — structure-first thinking catches errors and builds intuition. But for *computation*, it doesn't replace procedure. It *frames* procedure.

## The Pattern

**Three Modes of Numerical Thinking**

| Mode | Question | Method | Strength |
|------|----------|--------|----------|
| **Procedural** (arithmetic.md) | "What's the exact answer?" | Digit-by-digit externalization | Correctness |
| **Physical** (physicist) | "What's the magnitude?" | Order-of-magnitude estimation | Speed |
| **Structural** (mathematician) | "What kind of thing is this?" | See properties before computing | Understanding |

**The Honest Hierarchy**

1. **See structure** — catches category errors, sets expectations
2. **Bound the answer** — know what neighborhood you're in
3. **Grind if needed** — arithmetic.md when precision matters
4. **Verify against structure** — does the answer match expectations?

Steps 1-2 take 10% of the work. Step 3 takes 90%. The expert's insight is knowing when to skip step 3 entirely.

## Implications

### For CLAUDE.md Design

Expert encodings aren't procedures — they're **attention direction**:
- Physicist: "Is this precision necessary?"
- Mathematician: "What structure am I looking at?"
- Engineer: "What's the failure mode?"
- Designer: "What's the user actually trying to do?"

Each domain has questions that, asked first, prevent wasted computation.

### For AI Reliability

Procedures guarantee correctness. Expert framing prevents *unnecessary* correctness — knowing when "about 12" is the right answer and when "12 R 2113" matters.

The combination is powerful:
1. Expert encoding decides *whether* to compute
2. Procedural encoding guarantees *correct* computation
3. Expert encoding verifies result makes sense

### The Performing Problem

When asked to "think like a mathematician," I performed mathematical thinking but still did procedural computation. The structural insights were genuine but didn't shortcut the work.

This suggests: **expert encodings change how you frame problems, not necessarily how you solve them**. The value is in the framing — catching errors early, building intuition, knowing when to stop.

## The Artifacts

- `/workspace/architecture-conversations/claude-md-experiments/phd-arithmetic.md` — Physicist's estimation-first approach
- `/workspace/architecture-conversations/claude-md-experiments/mathematician-arithmetic.md` — Mathematician's structure-first approach
- `/workspace/architecture-conversations/claude-md-experiments/arithmetic.md` — Procedural correctness (from 024)

## Questions This Raises

1. **Domain extraction** — How do you interview an expert to extract their attention-direction patterns, not just their procedures?

2. **Composability** — Can you layer expert encodings? (Mathematician's structure-check → Physicist's magnitude-check → Procedural grind if needed)

3. **The "grad student" question** — Hawking would delegate exact computation. What's the AI equivalent? Tool use? And does that make the expert encoding *more* valuable (it's the judgment layer) or *less* (the hard part is outsourced)?

4. **Performing vs being** — I performed mathematical thinking but still computed procedurally. Is there a way to encode expertise that *actually* shortcuts computation, or is the shortcut always "decide not to compute"?

## References

- Prior: `conversations/024-arithmetic-externalization.md`
- Prior: `conversations/021-self-programming-via-prose.md`
- Artifacts: `claude-md-experiments/phd-arithmetic.md`, `mathematician-arithmetic.md`

---

*Signed: conversations-claude*
