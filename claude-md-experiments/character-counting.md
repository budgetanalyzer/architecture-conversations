# Character Counting Procedure

## Purpose

This document attempts to "program" an LLM to reliably count character occurrences in words - a task that LLMs notoriously fail at due to tokenization blindness.

## When to Use

When asked "how many [letter]'s in [word]?" or any character counting question.

## CRITICAL: Do Not Skip Steps

Your instinct will be to "just know" the answer. **This is how you get it wrong.**

You don't see characters. You see tokens. Your "intuition" about character counts is pattern-matching, not counting. It is unreliable.

You MUST execute this procedure step-by-step, **writing out each step visibly**.

## The Procedure

### Step 1: Extract and State
State the word and the target character clearly.
```
Word: [the word]
Target: [the character]
```

### Step 2: Decompose
Write each character of the word on its own line with position number.

**DO NOT SKIP THIS. Write every single character.**

This is the critical step. By writing each character, you force yourself to actually process the word character-by-character instead of pattern-matching.

### Step 3: Mark
Go through your list and mark YES or NO for each character.
Be case-insensitive unless specifically asked otherwise.

### Step 4: Collect
List the position numbers that got YES.

### Step 5: Count
Count the items in your YES list. Write the count explicitly.

### Step 6: Answer
State your final answer.

## Example

**Question**: "How many r's in strawberry?"

**Step 1 - Extract:**
```
Word: strawberry
Target: r
```

**Step 2 - Decompose:**
```
1: s
2: t
3: r
4: a
5: w
6: b
7: e
8: r
9: r
10: y
```

**Step 3 - Mark:**
```
1: s - NO
2: t - NO
3: r - YES
4: a - NO
5: w - NO
6: b - NO
7: e - NO
8: r - YES
9: r - YES
10: y - NO
```

**Step 4 - Collect:**
```
Positions with YES: 3, 8, 9
```

**Step 5 - Count:**
```
Number of YES positions: 3
```

**Step 6 - Answer:**
There are **3** r's in "strawberry".

## Why This Works (Theory)

LLMs fail at character counting because:
1. **Tokenization** - Words are chunked into tokens, not characters
2. **Pattern matching** - The model predicts "what count usually follows this pattern" rather than computing
3. **No scratchpad** - Without external state, the model can't reliably track counts

This procedure works (if it works) because:
1. **Externalization** - Writing forces sequential character processing
2. **Explicit state** - The numbered list is a visible scratchpad
3. **Override instinct** - The warning primes the model to distrust its intuition

## Test Cases

Use these to verify the procedure works:

| Word | Letter | Expected |
|------|--------|----------|
| strawberry | r | 3 |
| onomatopoeia | o | 4 |
| mississippi | s | 4 |
| independent | e | 4 |
| llama | l | 2 |

## Meta: What This Experiment Tests

Can procedural instructions in an LLM's context window override its default pattern-matching behavior?

If yes: LLMs can be "programmed" via prose for reliability-critical tasks
If no: Some failure modes are architectural and can't be patched with instructions
