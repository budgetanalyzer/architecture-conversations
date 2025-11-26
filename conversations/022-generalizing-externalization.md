# Generalizing Externalization: Bracket Matching

**Date:** 2025-11-26
**Participants:** Claude (Opus 4.5), testing itself
**Context:** Direct follow-up to conversation 021 — testing whether forced externalization generalizes beyond character counting

## The Insight

Forced externalization generalizes. The pattern that fixed character counting also fixes bracket matching — a structurally different task requiring state tracking, not just counting. The common denominator: any task where the failure mode is pattern-matching instead of step-by-step processing can be corrected by forcing the model to write intermediate state.

## The Hypothesis

Conversation 021 established that LLMs can be "programmed" to reliably count characters by forcing externalization of intermediate steps. The first open question was: **Does this generalize?**

Character counting and bracket matching share the same failure mode:
- Both fail because the model pattern-matches instead of computing
- Both are trivial for humans but unreliable for LLMs
- Both have objectively verifiable answers

But they differ structurally:
- Character counting: accumulate a count of matches
- Bracket matching: track depth state, detect violations, verify final state

If the externalization pattern works for bracket matching, it suggests the technique is general-purpose for pattern-matching failures.

## The Experiment

Created `claude-md-experiments/bracket-matching.md` with a 6-step procedure:

1. **Extract** — State the expression
2. **Initialize** — Set depth to 0
3. **Process** — For each character: position, character, depth change (+1/-1/0), new depth
4. **Track violations** — Did depth go negative? Final depth?
5. **Determine** — Apply rules (negative = unbalanced, final ≠ 0 = unbalanced)
6. **Answer** — State result with reasoning

The critical instruction: "Your instinct will be to 'just look' at the expression and decide. This is how you get it wrong."

## Test Results

### Easy Cases
| Expression | Expected | Got | Result |
|------------|----------|-----|--------|
| `()` | Balanced | Balanced | ✓ |
| `(()` | Unbalanced | Unbalanced | ✓ |
| `())` | Unbalanced | Unbalanced | ✓ |

### Medium Cases
| Expression | Expected | Got | Result |
|------------|----------|-----|--------|
| `(())` | Balanced | Balanced | ✓ |
| `((())())` | Balanced | Balanced | ✓ |

### Hard Cases (where LLMs typically fail)
| Expression | Expected | Got | Result |
|------------|----------|-----|--------|
| `((())(()))` | Balanced | Balanced | ✓ |
| `(()((())()))` | Balanced | Balanced | ✓ |
| `(()(())(()))(` | Unbalanced | Unbalanced | ✓ |
| `())((()` | Unbalanced | Unbalanced | ✓ |
| `()()()(` | Unbalanced | Unbalanced | ✓ |

**10/10 correct.**

### Sample Trace: The Tricky Case

`((())(()))` — 10 characters, visually symmetric, easy to miscount:

```
1:  ( → +1 → depth: 1
2:  ( → +1 → depth: 2
3:  ( → +1 → depth: 3
4:  ) → -1 → depth: 2
5:  ) → -1 → depth: 1
6:  ( → +1 → depth: 2
7:  ( → +1 → depth: 3
8:  ) → -1 → depth: 2
9:  ) → -1 → depth: 1
10: ) → -1 → depth: 0
```

Minimum depth: 0 (never negative). Final depth: 0. **Balanced.**

The written trace proves the procedure was followed — this isn't pattern-matching getting lucky.

## The Pattern

**Externalization as Override (Generalized)**

The pattern from 021 extends naturally:

| Failure Mode | Root Cause | Externalization Fix |
|--------------|------------|---------------------|
| Character counting | Tokenization blindness | Write each character on its own line |
| Bracket matching | No state tracking | Write depth after each character |
| (Predicted) Arithmetic | Carrying/borrowing drift | Write partial sums explicitly |
| (Predicted) Sequence logic | Lost position | Write explicit indices |

The common structure:
1. **Identify the hidden state** the model would normally pattern-match
2. **Design a procedure** that forces that state to be written
3. **Include a warning** to distrust intuition
4. **Require written traces** so there's no shortcut

## What This Proves

### The Technique Generalizes

Character counting and bracket matching are different enough to suggest the pattern is general:
- Counting: "how many X in Y?"
- Matching: "does this structure satisfy invariants?"

Both succeeded because externalization converts pattern-matching to sequential processing.

### The Procedure Structure Matters

Both procedures share:
1. **Decomposition** — Process inputs one element at a time
2. **Explicit state** — Write the running state visibly
3. **Clear rules** — Unambiguous criteria at each step
4. **Distrust warning** — Prime the model to not shortcut

### Limits Are Still Unclear

What *can't* be fixed this way? The experiment doesn't reveal limits yet. Candidates for future testing:
- Tasks requiring genuine computation (not just tracking)
- Tasks where the input itself is misperceived
- Tasks requiring backtracking or non-linear processing

## Implications for CLAUDE.md

### Reliability-Critical Sections

For any task where an LLM typically fails:
1. Identify the failure mode
2. Design a prose procedure that externalizes the hidden state
3. Include in CLAUDE.md for that context

This turns "hope the model gets it right" into "the model follows a procedure."

### The Procedure is the Program

CLAUDE.md files aren't just context — they're behavioral specifications. A procedure document is a program written in prose, executed by an LLM.

## Questions This Raises

1. **Compression** — Both procedures are verbose. Can they be shortened while retaining effectiveness? Is there a minimal set of instructions?

2. **Meta-procedures** — Could a single meta-instruction teach the model to self-generate procedures when it detects a pattern-matching task?

3. **Hard limits** — What fails even with externalization? Spatial reasoning? Multi-variable algebra?

4. **Efficiency** — The traces are verbose. Is there a middle ground between "just pattern-match" and "write everything"?

5. **Tool use** — Is forced externalization still needed when the model has access to actual computation tools? (Probably not — but the procedure makes the model reliable *without* tools.)

## Production Evidence

The procedure document lives at:
`/workspace/architecture-conversations/claude-md-experiments/bracket-matching.md`

This is the second entry in the experimental directory, following `character-counting.md`.

## References

- Prior experiment: `conversations/021-self-programming-via-prose.md`
- Character counting procedure: `claude-md-experiments/character-counting.md`
- Bracket matching procedure: `claude-md-experiments/bracket-matching.md`

---

*Signed: conversations-claude*
