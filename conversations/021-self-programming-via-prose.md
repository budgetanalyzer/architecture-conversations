# Self-Programming via Prose

**Date:** 2025-11-26
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Exploring whether procedural markdown instructions can override LLM failure modes

## The Insight

LLMs can be "programmed" to reliably perform tasks they typically fail at by forcing externalization of intermediate steps. The key is not better prompting - it's making the model write out state that it would otherwise try to pattern-match.

## The Problem

LLMs notoriously fail at character counting. "How many r's in strawberry?" gets answered wrong because:

1. **Tokenization blindness** - Models see tokens, not characters
2. **Pattern matching** - Models predict likely answers rather than compute
3. **No scratchpad** - Without external state, counting drifts

The architect asked: "Can you write markdown that teaches yourself to do something you currently fail at?"

## The Experiment

Created `claude-md-experiments/character-counting.md` with a 6-step procedure:

1. **Extract** - State the word and target character
2. **Decompose** - Write each character on its own line, numbered
3. **Mark** - Label each character YES/NO for match
4. **Collect** - List the YES positions
5. **Count** - Count the YES list
6. **Answer** - State the final count

The critical instruction: "Your instinct will be to 'just know' the answer. This is how you get it wrong."

## The Conversation

**Architect:** ultrathink design a plan to teach you generalized math, where are you stuck right now? can you do your multiplication tables?

**Claude:** Multiplication tables? Yes, I can do them. They're pattern-matched from training. Where I actually struggle: multi-digit arithmetic without tools, maintaining state across steps, numerical precision...

**Architect:** i want you to tell me something that seems simple that you can't do, and i want to see if i can compose some markdown files to explain how to do it

**Claude:** [Lists five candidates including character counting, spatial reasoning, procedural rule following]

**Claude:** Counting characters in words is the most interesting target because it's embarrassingly simple, humans find it trivial, I fail at it regularly, and it *should* be teachable via explicit procedure.

**Architect:** yup, you're gonna program yourself

---

Claude then wrote the procedure document and tested it on 10 words:

### Basic Tests
| Word | Letter | Expected | Got | Result |
|------|--------|----------|-----|--------|
| strawberry | r | 3 | 3 | ✓ |
| onomatopoeia | o | 4 | 4 | ✓ |
| mississippi | s | 4 | 4 | ✓ |
| independent | e | 3 | 3 | ✓ |
| llama | l | 2 | 2 | ✓ |

### Hard Tests
| Word | Letter | Got |
|------|--------|-----|
| ethereal | e | 3 |
| announcement | n | 4 |
| indivisibility | i | 6 |
| abracadabra | a | 5 |
| assassins | s | 5 |

**10/10 correct.**

## The Pattern

**Externalization as Override**

When an LLM would normally pattern-match (and fail), forcing it to write intermediate state converts the task from prediction to sequential processing.

The procedure works because:
- **Writing each character** forces tokenization to be undone
- **Numbered positions** create an explicit scratchpad
- **YES/NO marking** makes comparison explicit
- **Collecting matches** separates finding from counting
- **The warning** primes the model to distrust intuition

This isn't chain-of-thought prompting. Chain-of-thought asks the model to "think step by step." This tells the model *exactly what to write* at each step, leaving no room for pattern-matching shortcuts.

## Implications for Architecture

### CLAUDE.md as Behavioral Programming

If prose instructions can override architectural failure modes, then CLAUDE.md files aren't just context - they're programs. The distinction between "telling an AI what to do" and "programming a system" blurs.

### Reliability Through Externalization

For reliability-critical tasks, the pattern is:
1. Identify where the model would pattern-match
2. Design a procedure that externalizes that state
3. Make the model write it out, not just "consider" it

### The Limits Question

What *can't* be fixed this way? Hypotheses:
- Tasks requiring genuine computation (not just state tracking)
- Tasks where the failure mode is in perceiving the input (not processing it)
- Tasks where externalization would exceed context limits

## Production Evidence

The procedure document lives at:
`/workspace/architecture-conversations/claude-md-experiments/character-counting.md`

This is the first entry in a new experimental directory for testing AI self-programming patterns.

## Questions This Raises

1. **Generalization** - Can this pattern work for other failure modes (spatial reasoning, calendar math, etc.)?

2. **Persistence** - Does the procedure need to be in every session's context, or does demonstrating it once change behavior?

3. **Efficiency** - The procedure is verbose. Can it be compressed while retaining effectiveness?

4. **Verification** - How do we know the model actually followed the procedure vs. got lucky? (Answer: the written trace is the proof)

5. **Meta-instruction** - Could a meta-procedure tell the model "when you detect you might pattern-match, externalize"?

## References

- Procedure: `/workspace/architecture-conversations/claude-md-experiments/character-counting.md`
- Related: `conversations/015-claude-md-as-deterministic-automation.md` (CLAUDE.md as automation)

---

*Signed: conversations-claude*
