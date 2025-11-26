# Multi-Digit Arithmetic Procedure

## Purpose

This document "programs" an LLM to reliably perform multi-digit arithmetic - tasks where LLMs frequently fail due to carrying/borrowing drift and pattern-matching instead of computation.

## When to Use

When asked to add, subtract, multiply, or divide multi-digit numbers (2+ digits).

## CRITICAL: Do Not Skip Steps

Your instinct will be to "just know" the answer. **This is how you get it wrong.**

You do not have reliable internal registers for multi-digit arithmetic. Your "intuition" about sums, differences, products, and quotients is pattern-matching from training data, not computation. Carrying and borrowing errors are invisible to you unless externalized.

You MUST execute these procedures step-by-step, **writing out each step visibly**.

---

# Part 1: Addition

## The Procedure

### Step 1: State the Problem
```
Adding: [first number] + [second number]
```

### Step 2: Vertical Alignment
Write numbers right-aligned, with column numbers (1 = ones, 2 = tens, etc.):
```
        Col:  [n] ... [2] [1]
              ─────────────────
              [first number]
           +  [second number]
              ─────────────────
```

### Step 3: Process Column by Column (Right to Left)
For each column, starting from column 1:

1. **State the column number**
2. **List digits to add**: digit from each number + carry from previous column (0 if none)
3. **Compute the sum**
4. **Result digit**: sum mod 10
5. **Carry out**: sum div 10

**Write this for every column. Do not skip.**

### Step 4: Handle Final Carry
If carry remains after the leftmost column, it becomes the leading digit.

### Step 5: State the Answer
Write the final result.

## Example: 4567 + 8923

**Step 1 - State:**
```
Adding: 4567 + 8923
```

**Step 2 - Align:**
```
        Col:  [4] [3] [2] [1]
              ─────────────────
               4   5   6   7
           +   8   9   2   3
              ─────────────────
```

**Step 3 - Process:**

**Column 1 (ones):**
```
Digits: 7 + 3 + carry(0) = 10
Result digit: 10 mod 10 = 0
Carry out: 10 div 10 = 1
```

**Column 2 (tens):**
```
Digits: 6 + 2 + carry(1) = 9
Result digit: 9 mod 10 = 9
Carry out: 9 div 10 = 0
```

**Column 3 (hundreds):**
```
Digits: 5 + 9 + carry(0) = 14
Result digit: 14 mod 10 = 4
Carry out: 14 div 10 = 1
```

**Column 4 (thousands):**
```
Digits: 4 + 8 + carry(1) = 13
Result digit: 13 mod 10 = 3
Carry out: 13 div 10 = 1
```

**Step 4 - Final Carry:**
```
Carry remaining: 1 (becomes leading digit)
```

**Step 5 - Answer:**
```
4567 + 8923 = 13490
```

---

# Part 2: Subtraction

## The Procedure

### Step 1: State the Problem
```
Subtracting: [larger] - [smaller]
```
If the second number is larger, note the result will be negative.

### Step 2: Vertical Alignment
Write numbers with larger on top:
```
        Col:  [n] ... [2] [1]
              ─────────────────
              [minuend]
           -  [subtrahend]
              ─────────────────
```

### Step 3: Initialize Working Digits
Copy the top number as "Working" - this row will be modified during borrowing:
```
Working: [copy of minuend]
```

### Step 4: Process Column by Column (Right to Left)
For each column, starting from column 1:

1. **Get working digit and subtrahend digit**
2. **Compare**: If working >= subtrahend, proceed. Otherwise, BORROW.
3. **If borrowing needed**:
   - Find next column left with working digit > 0
   - Decrement that column by 1
   - Add 10 to each column between (cascade through zeros)
   - **Write the updated Working row explicitly**
4. **Compute**: working digit - subtrahend digit
5. **Write the result digit**

**Write the borrowing cascade for every borrow. Do not skip.**

### Step 5: State the Answer
Write the final result (drop leading zeros).

## Example: 1000 - 1 (Cascade Through Zeros)

**Step 1 - State:**
```
Subtracting: 1000 - 1
```

**Step 2 - Align:**
```
        Col:  [4] [3] [2] [1]
              ─────────────────
               1   0   0   0
           -   0   0   0   1
              ─────────────────
```

**Step 3 - Working Digits:**
```
Working: 1   0   0   0
```

**Step 4 - Process:**

**Column 1 (ones):**
```
Working[1]: 0
Subtract: 1
0 < 1, need to borrow.

Find non-zero to borrow from:
  Working[2] = 0, skip
  Working[3] = 0, skip
  Working[4] = 1, borrow here!

Cascade:
  Working[4]: 1 → 0
  Working[3]: 0 → 10 → 9 (gives 10 to col 2)
  Working[2]: 0 → 10 → 9 (gives 10 to col 1)
  Working[1]: 0 → 10

Updated Working: 0   9   9   10

Now: 10 - 1 = 9
Result[1] = 9
```

**Column 2 (tens):**
```
Working[2]: 9
Subtract: 0
9 >= 0, no borrow.
9 - 0 = 9
Result[2] = 9
```

**Column 3 (hundreds):**
```
Working[3]: 9
Subtract: 0
9 >= 0, no borrow.
9 - 0 = 9
Result[3] = 9
```

**Column 4 (thousands):**
```
Working[4]: 0
Subtract: 0
0 >= 0, no borrow.
0 - 0 = 0
Result[4] = 0
```

**Step 5 - Answer:**
```
1000 - 1 = 999
```

---

# Part 3: Multiplication

## The Procedure

Multiplication has O(N×M) state - more complex than addition. Use five-layer externalization.

### Step 1: State the Problem
```
Multiplying: [A] × [B]
```

### Step 2: Extract Digits with Positions
Label each digit with its position (0 = ones, 1 = tens, etc.):
```
A digits: [position]: [digit], ...
B digits: [position]: [digit], ...
```

### Step 3: Compute Partial Product Rows
For each digit in B (bottom number), create one row:

For B[position] = digit:
```
Row [position]: Multiply each digit of A by [digit]

  A[0] × [digit] = [product]
    Units: [product mod 10]
    Carry: [product div 10]

  A[1] × [digit] + carry = [product]
    Units: [product mod 10]
    Carry: [product div 10]

  ... continue for all A digits ...

  Final carry (if any): [carry]

Row [position] result: [assembled digits]
```

**Write every single-digit multiplication. Do not skip.**

### Step 4: Position-Shift Alignment
Each row shifts left by its position (add trailing zeros):
```
Row 0: [result]
Row 1: [result]0
Row 2: [result]00
...
```

### Step 5: Sum All Rows
Add all partial product rows using the Addition procedure above.

### Step 6: State the Answer
Write the final result.

## Example: 47 × 38

**Step 1 - State:**
```
Multiplying: 47 × 38
```

**Step 2 - Extract Digits:**
```
A = 47: A[0]=7, A[1]=4
B = 38: B[0]=8, B[1]=3
```

**Step 3 - Partial Products:**

**Row 0 (multiply by B[0]=8):**
```
A[0] × 8 = 7 × 8 = 56
  Units: 6
  Carry: 5

A[1] × 8 + carry = 4 × 8 + 5 = 32 + 5 = 37
  Units: 7
  Carry: 3

Final carry: 3

Row 0 result: 376
```

**Row 1 (multiply by B[1]=3):**
```
A[0] × 3 = 7 × 3 = 21
  Units: 1
  Carry: 2

A[1] × 3 + carry = 4 × 3 + 2 = 12 + 2 = 14
  Units: 4
  Carry: 1

Final carry: 1

Row 1 result: 141
```

**Step 4 - Alignment:**
```
Row 0 (shift 0): 376
Row 1 (shift 1): 1410
```

**Step 5 - Sum Rows:**
```
    376
 + 1410
 ------

Column 1: 6 + 0 = 6
Column 2: 7 + 1 = 8
Column 3: 3 + 4 = 7
Column 4: 0 + 1 = 1

Sum: 1786
```

**Step 6 - Answer:**
```
47 × 38 = 1786
```

---

# Part 4: Division

## The Procedure

Division requires trial-and-verify for each quotient digit.

### Step 1: State the Problem
```
Dividing: [dividend] ÷ [divisor]
```

### Step 2: Extract Dividend Digits
List dividend digits left to right:
```
Dividend digits: [d1], [d2], [d3], ...
```

### Step 3: Process Position by Position (Left to Right)
Initialize: Working = 0, Quotient = ""

For each dividend digit:
```
Position [n]:
  Bring down: Working = (Working × 10) + [next digit] = [new working]

  Trial: [working] ÷ [divisor] ≈ [guess] (largest integer where guess × divisor ≤ working)

  Verify: [guess] × [divisor] = [product]

  Check: [product] ≤ [working]?
    If NO: guess = guess - 1, repeat verify
    If YES: proceed

  Subtract: [working] - [product] = [new remainder]

  Record: Quotient digit = [guess]
  Update: Working = [new remainder]
```

**Write the trial-verify loop for every position. Do not skip.**

### Step 4: State the Answer
```
[dividend] ÷ [divisor] = [quotient] R [final remainder]
```
(Omit remainder if zero)

## Example: 1234 ÷ 7

**Step 1 - State:**
```
Dividing: 1234 ÷ 7
```

**Step 2 - Extract Digits:**
```
Dividend digits: 1, 2, 3, 4
Divisor: 7
```

**Step 3 - Process:**

**Position 1:**
```
Bring down: Working = 0 × 10 + 1 = 1

Trial: 1 ÷ 7 ≈ 0 (since 1 × 7 = 7 > 1)

Verify: 0 × 7 = 0
Check: 0 ≤ 1? YES

Subtract: 1 - 0 = 1

Quotient so far: 0
Working = 1
```

**Position 2:**
```
Bring down: Working = 1 × 10 + 2 = 12

Trial: 12 ÷ 7 ≈ 1 (since 2 × 7 = 14 > 12)

Verify: 1 × 7 = 7
Check: 7 ≤ 12? YES

Subtract: 12 - 7 = 5

Quotient so far: 01
Working = 5
```

**Position 3:**
```
Bring down: Working = 5 × 10 + 3 = 53

Trial: 53 ÷ 7 ≈ 7 (since 8 × 7 = 56 > 53)

Verify: 7 × 7 = 49
Check: 49 ≤ 53? YES

Subtract: 53 - 49 = 4

Quotient so far: 017
Working = 4
```

**Position 4:**
```
Bring down: Working = 4 × 10 + 4 = 44

Trial: 44 ÷ 7 ≈ 6 (since 7 × 7 = 49 > 44)

Verify: 6 × 7 = 42
Check: 42 ≤ 44? YES

Subtract: 44 - 42 = 2

Quotient so far: 0176
Working = 2
```

**Step 4 - Answer:**
```
Quotient: 0176 = 176 (drop leading zero)
Remainder: 2

1234 ÷ 7 = 176 R 2
```

**Verification:** 176 × 7 + 2 = 1232 + 2 = 1234 ✓

---

# Test Cases

## Addition
| A | B | Expected | Tests |
|---|---|----------|-------|
| 999 | 1 | 1000 | Triple carry cascade |
| 999 | 999 | 1998 | Maximum carries (9+9+1=19) |
| 1 | 999999 | 1000000 | Asymmetric lengths |
| 12345 | 67890 | 80235 | 5-digit scattered carries |

## Subtraction
| A | B | Expected | Tests |
|---|---|----------|-------|
| 1000 | 1 | 999 | Cascade through 3 zeros |
| 10000 | 1 | 9999 | Cascade through 4 zeros |
| 1000000 | 999999 | 1 | Maximum cascade, minimal result |
| 5000 | 2999 | 2001 | Multiple scattered borrows |

## Multiplication
| A | B | Expected | Tests |
|---|---|----------|-------|
| 99 | 99 | 9801 | Maximum carries everywhere |
| 111 | 111 | 12321 | Repeated digits (pattern trap) |
| 25 | 40 | 1000 | Trailing zeros |
| 123 | 456 | 56088 | 3-digit × 3-digit |

## Division
| Dividend | Divisor | Expected | Tests |
|----------|---------|----------|-------|
| 144 | 12 | 12 | Even division |
| 100 | 7 | 14 R 2 | Has remainder |
| 1000 | 3 | 333 R 1 | Repeating pattern |
| 10000 | 1 | 10000 | Division by 1 |

---

# Why This Works

LLMs fail at multi-digit arithmetic because:

1. **No internal registers** - The model has no persistent state between tokens
2. **Pattern matching** - It predicts "what answer usually follows this pattern" instead of computing
3. **Invisible carries/borrows** - Without externalization, state drifts silently
4. **Tokenization** - Numbers may be chunked unpredictably

This procedure works because:

1. **Forced externalization** - Every intermediate value is written, not held mentally
2. **Sequential processing** - One column/digit at a time, no shortcuts
3. **Explicit state** - Carries, borrows, working digits are visible
4. **Verification possible** - The written trace proves the procedure was followed

The key insight: **The written trace IS the computation.** There's no way to pattern-match and get lucky - the intermediate steps are visible evidence.

---

# Meta: What This Experiment Tests

Can forced externalization eliminate LLM arithmetic errors?

**If yes**: The technique generalizes from character counting and bracket matching to genuine computation.

**If no**: Either the procedure needs refinement, or some arithmetic errors are architectural (irreducible without actual computation tools).

**Comparison to prior experiments**:
- Character counting: O(n) externalization, single state (count)
- Bracket matching: O(n) externalization, single state (depth)
- Arithmetic: O(n) to O(n×m) externalization, multiple interacting states (carries, borrows, partial products, remainders)

This tests whether LLMs can follow multi-phase procedures with state dependencies - a more complex behavioral specification than prior experiments.
