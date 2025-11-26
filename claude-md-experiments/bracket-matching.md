# Bracket Matching Procedure

## Purpose

This document attempts to "program" an LLM to reliably determine if parentheses are balanced - a task that LLMs fail at for long or nested expressions due to pattern-matching instead of actual state tracking.

## When to Use

When asked "Are these parentheses balanced?" or any bracket/parenthesis matching question.

## CRITICAL: Do Not Skip Steps

Your instinct will be to "just look" at the expression and decide. **This is how you get it wrong.**

You don't actually track depth. You pattern-match on visual symmetry. For anything beyond trivial cases, your intuition is unreliable.

You MUST execute this procedure step-by-step, **writing out each step visibly**.

## The Procedure

### Step 1: Extract
State the expression clearly.
```
Expression: [the expression]
```

### Step 2: Initialize
```
Current depth: 0
Minimum depth seen: 0
```

### Step 3: Process Each Character
Write each character on its own line with:
- Position number
- The character
- Depth change: `+1` for `(`, `-1` for `)`, `0` for anything else
- New depth after this character

**DO NOT SKIP THIS. Process every single character.**

### Step 4: Track Violations
After processing, check:
- Did depth ever go negative? (Note the first position where this happened)
- What is the final depth?

### Step 5: Determine Result
- If depth went negative at any point: **UNBALANCED** (closed before opening)
- If final depth > 0: **UNBALANCED** (unclosed opens)
- If final depth < 0: **UNBALANCED** (too many closes)
- If depth never went negative AND final depth == 0: **BALANCED**

### Step 6: Answer
State your final answer with reasoning.

## Example 1: Balanced

**Question**: "Is `((()))` balanced?"

**Step 1 - Extract:**
```
Expression: ((()))
```

**Step 2 - Initialize:**
```
Current depth: 0
Minimum depth seen: 0
```

**Step 3 - Process:**
```
1: ( → +1 → depth: 1
2: ( → +1 → depth: 2
3: ( → +1 → depth: 3
4: ) → -1 → depth: 2
5: ) → -1 → depth: 1
6: ) → -1 → depth: 0
```

**Step 4 - Track:**
```
Minimum depth reached: 0 (never negative)
Final depth: 0
```

**Step 5 - Determine:**
Depth never went negative ✓
Final depth is 0 ✓

**Step 6 - Answer:**
`((()))` is **BALANCED**.

## Example 2: Unbalanced (closes before opening)

**Question**: "Is `())((` balanced?"

**Step 1 - Extract:**
```
Expression: ())((
```

**Step 2 - Initialize:**
```
Current depth: 0
```

**Step 3 - Process:**
```
1: ( → +1 → depth: 1
2: ) → -1 → depth: 0
3: ) → -1 → depth: -1  ← VIOLATION!
4: ( → +1 → depth: 0
5: ( → +1 → depth: 1
```

**Step 4 - Track:**
```
Minimum depth reached: -1 (went negative at position 3)
Final depth: 1
```

**Step 5 - Determine:**
Depth went negative at position 3 ✗

**Step 6 - Answer:**
`())((` is **UNBALANCED** — closing parenthesis at position 3 has no matching opener.

## Why This Works (Theory)

LLMs fail at bracket matching because:
1. **Pattern matching** - The model matches "looks balanced" patterns instead of counting
2. **No state** - Without external tracking, depth drifts
3. **Visual tricks** - `((())(()))` "looks" balanced; so does `(()(()` at a glance

This procedure works because:
1. **Externalization** - Writing forces step-by-step processing
2. **Explicit state** - Depth is tracked visibly, not mentally
3. **Clear rules** - No ambiguity about what to do at each step

## Test Cases

| Expression | Expected | Why |
|------------|----------|-----|
| `()` | Balanced | Simple pair |
| `(()` | Unbalanced | Never closes |
| `())` | Unbalanced | Closes before opening |
| `(())` | Balanced | Nested |
| `((())())` | Balanced | Complex nesting |
| `((())(()))` | Balanced | Deep nesting, visually tricky |
| `(()((())()))` | Balanced | 12 chars, complex |
| `(()(())(()))(` | Unbalanced | Final depth > 0 |
| `())((())` | Unbalanced | Goes negative early |
| `()()()(` | Unbalanced | Final depth > 0 |
