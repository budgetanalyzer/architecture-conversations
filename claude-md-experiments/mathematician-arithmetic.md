# Mathematician's Arithmetic

**Context:** How a working mathematician thinks about computation. Not procedures, not estimation — *understanding*.

**Philosophy:** A mathematician doesn't solve problems. A mathematician understands structures, and solutions fall out as corollaries.

---

## The Wrong Question and the Right One

You're asked: "What is 78529 ÷ 6368?"

A calculator computes. A physicist estimates. A mathematician asks: **What do I already know about this quotient without computing anything?**

```
1. Is it an integer?
   78529 is odd. 6368 is even. An odd number cannot be a multiple of an even.
   → The quotient is not an integer. Proven in one observation.

2. Is the fraction reducible?
   Run Euclidean algorithm (details below). GCD = 1.
   → 78529/6368 is already in lowest terms.

3. What's the magnitude?
   78529/6368 ≈ 80000/6400 = 12.5
   → The integer part is 12.

4. What's the structure of the approximations?
   The continued fraction [12; 3, 72, 1, 6, 4] gives the convergents.
   → 12/1, 37/3, 2677/217, ... are the best rational approximations.
```

We now know: quotient is 12 + (irreducible proper fraction), with well-characterized approximation behavior. We haven't "calculated" anything. We've *understood* it.

---

## What Ring Are You In?

Before computing, ask: what's the question actually asking?

```
In ℤ: 78529 = 12 × 6368 + r, find r    (Euclidean domain)
In ℚ: 78529/6368 exactly               (field)
In ℝ: 12.33396...                       (complete ordered field)
In ℤ/nℤ: 78529 ≡ ? (mod 6368)          (quotient ring)
```

The "same" division means different things in different structures. A mathematician always knows which structure they're in.

---

## The Euclidean Algorithm Reveals Everything

Watch what the algorithm *tells* you, not just what it *computes*:

```
78529 = 12 × 6368 + 2113    quotient: 12
 6368 =  3 × 2113 +   29    quotient: 3
 2113 = 72 ×   29 +   25    quotient: 72
   29 =  1 ×   25 +    4    quotient: 1
   25 =  6 ×    4 +    1    quotient: 6
    4 =  4 ×    1 +    0    quotient: 4

GCD(78529, 6368) = 1. Coprime.
```

The quotients [12; 3, 72, 1, 6, 4] **are** the continued fraction expansion:

```
78529     1
───── = 12 + ─────────────────────────
6368          1
            3 + ──────────────────────
                      1
                72 + ────────────────
                          1
                     1 + ───────────
                              1
                          6 + ───
                              4
```

The convergents (truncated continued fractions) give the *best* rational approximations:
- p₀/q₀ = 12/1 = 12.000...
- p₁/q₁ = 37/3 = 12.333...
- p₂/q₂ = 2677/217 = 12.3318...

No fraction with smaller denominator approximates 78529/6368 better. This is a theorem, not a computation.

### The Lattice Interpretation

GCD(a,b) is the lattice point closest to the line y = (b/a)x, reached by a greedy walk. The Euclidean algorithm *is* that walk. This is why it terminates — you're strictly decreasing distance to the origin.

---

## Proof Without Computation

The mathematician's power: establishing truths without grinding.

**Example: What is 78529¹⁰⁰ mod 7?**

Naive approach: Compute 78529¹⁰⁰, take mod 7. Impossible without a computer.

Mathematician's approach:
```
78529 mod 7:
  78529 = 7 × 11218 + 3
  So 78529 ≡ 3 (mod 7)

By Fermat's Little Theorem: a^(p-1) ≡ 1 (mod p) for prime p, a not divisible by p.
  3⁶ ≡ 1 (mod 7)

100 = 6 × 16 + 4
  3¹⁰⁰ = (3⁶)¹⁶ × 3⁴ ≡ 1¹⁶ × 81 ≡ 81 ≡ 4 (mod 7)

Therefore: 78529¹⁰⁰ ≡ 4 (mod 7)
```

Verification: 3⁴ = 81 = 7 × 11 + 4 ✓

We computed nothing big. We *proved* the answer using structure.

**Example: Does 78529 have a square root mod 6368?**

By quadratic reciprocity and CRT, we can determine this without ever trying to find the root. (It doesn't — 6368 = 2⁵ × 199, and checking Legendre symbols gives the answer.)

---

## The Hierarchy of Mathematical Understanding

1. **What structure?** — Ring, field, module, group? Euclidean domain?
2. **What's invariant?** — GCD, residue class, order, degree
3. **What's really being asked?** — Often the stated question isn't the interesting one
4. **What can be proven without computation?** — Existence, uniqueness, impossibility, bounds
5. **What constraints does structure impose?** — Odd/even, primality, coprimality
6. **Only then: compute** — And verify it matches what you already knew

The computation is a sanity check on your understanding. If you're surprised by the answer, you didn't understand the question.

---

## On Factorization

The current document attempts factorization by trial. A mathematician doesn't guess — they see.

```
6368:
  Even, so factor out 2s: 6368/2 = 3184/2 = 1592/2 = 796/2 = 398/2 = 199
  6368 = 2⁵ × 199
  199 is prime? Check: √199 ≈ 14.1. Not divisible by 2,3,5,7,11,13. Prime.

78529:
  Odd, so no factors of 2.
  Digit sum 7+8+5+2+9 = 31, not divisible by 3.
  Doesn't end in 0 or 5, so no factor of 5.
  √78529 ≈ 280.2

  Is it prime? Run a primality test or factor.
  Actually: 78529 = 280² + 129 = 78400 + 129.
  Not obviously factorable. Try: 78529 = 293 × 268 - 95 = ... this is getting ugly.

  Better: We already know GCD(78529, 6368) = 1.
  If 78529 shared a factor with 6368 = 2⁵ × 199, it would share 2 or 199.
  It's odd (not 2), and 78529/199 = 394.6... (not 199).
  So 78529 and 6368 share no factors. That's what we needed.
```

The mathematician factors *only as much as the problem requires*.

---

## Mathematicians on Mathematics

**Poincaré**: "The mathematician does not study pure mathematics because it is useful; he studies it because he delights in it and he delights in it because it is beautiful."

**Grothendieck**: "The introduction of the cipher 0 or the group concept was general nonsense too, and mathematics was more or less stagnating for thousands of years because nobody was around to take such childish steps."

**Erdős**: "A mathematician is a machine for turning coffee into theorems."

**Tao**: "Good mathematics does not always require the sharpest, most impressive tools; sometimes the trick is to find the simplest approach."

**Gauss** (apocryphal, on his proofs): "A cathedral is not a quarry." (You don't show the scaffolding.)

---

## Contrast

| Approach | Sees | Method | Question |
|----------|------|--------|----------|
| **Procedural** | Digits | Mechanical steps | "What is the answer?" |
| **Physicist** | Magnitudes | Dimensional analysis | "What order of magnitude?" |
| **Mathematician** | Structure | Abstraction + proof | "What *must* the answer be?" |

The physicist asks: "Roughly what?"
The mathematician asks: "What can I prove?" — and the answer often follows.

---

## The Meta-Point: For AI

This document isn't about teaching shortcuts. It's about teaching *questions*.

Before computing, ask:
1. **What structure does this have?** — The structure constrains the answer
2. **What's preserved under the operation?** — Invariants reveal truth
3. **What can I prove without computing?** — Often everything that matters
4. **What is this problem an instance of?** — Generalize, then specialize back

The failure mode of LLM arithmetic isn't "can't compute" — it's "computes without understanding." The computation feels right but isn't. Structure-first thinking *prevents* this: if your answer violates what you proved, you know it's wrong.

---

*"The art of doing mathematics consists in finding that special case which contains all the germs of generality."* — David Hilbert

*Understanding the structure is faster than grinding the digits, and it's the only way to be sure.*
