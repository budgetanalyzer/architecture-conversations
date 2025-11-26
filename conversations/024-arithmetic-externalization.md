# Arithmetic Externalization: The Third Proof

**Date:** 2025-11-26
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Testing whether forced externalization scales to multi-digit arithmetic — O(n×m) complexity with interacting state (carries, borrows, partial products)

## The Insight

Forced externalization works for arithmetic, confirming the pattern generalizes beyond simple counting and state tracking. But it also reveals the gap between *correct* and *efficient* — the procedure is brute-force where a mathematician would use estimation shortcuts. The next frontier: can you encode mathematical *intuition*, not just mechanical procedures?

## The Experiment

Created `claude-md-experiments/arithmetic.md` with procedures for all four operations:

1. **Addition** — Column-by-column with explicit carries
2. **Subtraction** — Working digits row, cascade-through-zeros borrowing
3. **Multiplication** — Partial product rows, position-shifted alignment, sum
4. **Division** — Trial-and-verify for each quotient digit

Each procedure forces externalization of the state that would otherwise drift:
- Carries/borrows written explicitly
- Each column processed individually
- Running state visible at every step

The critical instruction (same as 021 and 022): "Your instinct will be to 'just know' the answer. **This is how you get it wrong.**"

## Live Test: Multiplication

**32354 × 5663**

Following the procedure produced 4 partial product rows, each with explicit carry tracking:

```
Row 0 (×3): 97062
Row 1 (×6): 194124 → shifted: 1941240
Row 2 (×6): 194124 → shifted: 19412400
Row 3 (×5): 161770 → shifted: 161770000
```

Summed with column-by-column addition:

```
32354 × 5663 = 183220702 ✓
```

## Live Test: Division

**345223332 ÷ 35452233**

Nine positions of trial-and-verify. Positions 1-8 all yielded quotient digit 0 (divisor larger than working value). Position 9 required:

1. **Trial**: 345223332 ÷ 35452233 ≈ 9 or 10?
2. **Verify 10**: 10 × 35452233 = 354522330 > 345223332, reject
3. **Verify 9**: 9 × 35452233 = 319070097 ≤ 345223332, accept
4. **Subtract**: 345223332 - 319070097 = 26153235

```
345223332 ÷ 35452233 = 9 R 26153235 ✓
```

Verification: 9 × 35452233 + 26153235 = 345223332 ✓

## The Conversation

**Architect:** [Reviews arithmetic.md, asks for analysis]

**Claude:** The diagnosis is accurate — LLMs pattern-match on arithmetic rather than compute. The externalization technique from 021 and 022 should transfer. But is this solving the right problem? For practical arithmetic, use tools. The interesting question is whether LLMs can be *specified into* reliable computation.

**Architect:** whats 32354x5663

**Claude:** [Executes full multiplication procedure, 4 partial products, column sums] = 183220702

**Architect:** 35452233/345223332

**Claude:** Wait — the divisor is larger than the dividend. That's 0 R 35452233.

**Architect:** sure [meaning: do it the other way]

**Claude:** [Executes full division procedure, 9 positions, trial-verify on position 9] = 9 R 26153235

**Architect:** yup, a real mathematician could get that arithmetic.md humming. we all have mental shortcuts right? divide by 100, work with the remainder. that's what this is about, explaining the process to ai so ai can scale

**Claude:** Exactly. The document is minimal viable procedure — guarantees correctness but doesn't encode the heuristics that make humans fast.

A mathematician looks at 345223332 ÷ 35452233 and thinks: "≈ 345M ÷ 35M ≈ 10. Check 10... too big. So 9." One mental operation versus nine-position grind.

The real research question: Can you encode mathematical *intuition* — not just mechanical procedures — in a way an LLM will reliably apply?

## The Pattern

**Complexity Hierarchy Confirmed**

| Experiment | State | Complexity | Status |
|------------|-------|------------|--------|
| Character counting (021) | Single counter | O(n) | ✓ |
| Bracket matching (022) | Single depth | O(n) | ✓ |
| Arithmetic (024) | Multiple interacting (carries, partial products) | O(n) to O(n×m) | ✓ |

Externalization scales. The technique handles multi-phase procedures with state dependencies.

**Correct vs. Efficient**

The procedure is bubble sort when quicksort exists. It's O(correct) but not O(efficient). The shortcuts that make experts fast are exactly the pattern-matching that makes LLMs unreliable.

Possible middle ground: **verified shortcuts**

```
Heuristic: 345M ÷ 35M ≈ 9 or 10
Verify: 10 × 35452233 = 354522330 > 345223332, reject
Verify: 9 × 35452233 = 319070097 ≤ 345223332, accept
```

Three operations instead of dozens. Same correctness guarantee.

## Implications

### For CLAUDE.md Design

When specifying procedures:
1. **Start with correct** — Brute-force that guarantees right answers
2. **Add heuristics** — Estimation shortcuts with verification steps
3. **The heuristic proposes, the procedure verifies**

### For AI Reliability

The atomic operations (single-digit multiplication, comparison) are still pattern-matched. Externalization isolates them so errors don't compound. The question isn't "can LLMs compute?" — it's "can LLMs follow procedures that decompose computation into reliable atoms?"

Answer: Yes.

### The Tool Question

Is forced externalization still needed when the model has computation tools? Probably not for arithmetic specifically. But the pattern matters for domains where tools don't exist — any task requiring reliable sequential processing of state.

## Questions This Raises

1. **Heuristic encoding** — Can estimation shortcuts be specified reliably, or does that reintroduce pattern-matching failures?

2. **Domain expertise** — A mathematician's CLAUDE.md would include factoring awareness, magnitude reasoning, boundary checking. How do you extract expert intuition into prose?

3. **Diminishing returns** — Character counting to bracket matching to arithmetic. What's next? Sorting? Graph traversal? Where does externalization hit limits?

4. **Token economics** — The division trace was expensive. Is there a compression that retains correctness?

## Production Evidence

- Procedure document: `claude-md-experiments/arithmetic.md`
- Test cases embedded in procedure (targeting known failure modes: cascade-through-zeros, maximum carries, trailing zeros)

## References

- Prior: `conversations/021-self-programming-via-prose.md` (character counting)
- Prior: `conversations/022-generalizing-externalization.md` (bracket matching)
- Procedure: `claude-md-experiments/arithmetic.md`

---

*Signed: conversations-claude*
