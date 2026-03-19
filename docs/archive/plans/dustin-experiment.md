# Experiment: The Dustin Test

## Hypothesis

An expert can externalize their domain knowledge through CLAUDE.md such that AI transforms from producing **garbage** to producing **expert-level output** on the same prompt.

## The Subject

**Dustin** - Driver developer at NVIDIA. Domain expertise that Claude doesn't have deep knowledge of.

## The Experiment

### Phase 1: The Before Test

1. Dustin picks a driver development problem he recently solved (something real, something complex)
2. Dustin clones Bleu's repo (architecture-conversations)
3. Dustin runs his driver prompt against this context
4. **Expected result:** Garbage (wrong domain context loaded)

### Phase 2: The Externalization

1. Dustin works with Claude to solve the problem through dialogue
2. As they work, Dustin externalizes his thinking into CLAUDE.md
3. This includes:
   - How he approaches driver problems
   - What he looks for first
   - Common pitfalls he knows about
   - Decision frameworks he uses
   - NVIDIA-specific knowledge

### Phase 3: The After Test

1. Dustin posts his CLAUDE.md at a new URL (his own repo)
2. Runs the SAME prompt against HIS context
3. **Expected result:** Expert-level output (his thinking is now in context)

### Phase 4: The Comparison

Side-by-side: same prompt, two contexts
- Before: Bleu's microservices context → garbage for driver problems
- After: Dustin's driver context → coherent expert output

## Success Criteria

**Clear success:**
- Before output is obviously wrong/generic
- After output reflects Dustin's actual approach
- Dustin recognizes "that's how I think about this" in the after output

**Bonus success:**
- Dustin has the "aha" moment about the pattern itself
- Dustin wants to expand his CLAUDE.md for other driver problems

## What This Proves

1. **Domain-agnostic** - Pattern works outside microservices/web dev
2. **Expert-driven** - The expert's thinking shapes the output
3. **Measurable** - Before/after comparison is concrete
4. **Reproducible** - Anyone with expertise can do this in their domain

## What We Need From Dustin

1. **A problem** - Something he recently solved, complex enough that vanilla Claude would fail
2. **Time** - To work through the externalization process
3. **A repo** - Somewhere to post his CLAUDE.md

## Preparation Checklist

- [ ] Contact Dustin, explain the experiment
- [ ] Have him pick a real driver problem
- [ ] Set up the before test (clone architecture-conversations)
- [ ] Run before test, capture output
- [ ] Guide the externalization process
- [ ] Set up after test (his repo with his CLAUDE.md)
- [ ] Run after test, capture output
- [ ] Compare and document

## The Pitch to Dustin

> "Hey, I've been working on this pattern for externalizing expert knowledge into AI context. Want to test if it works for driver development?
>
> The experiment: You pick a hard driver problem. We run it against my repo first (should fail - wrong domain). Then you work with Claude to solve it while documenting your thinking. We run the same problem against YOUR context. If the output goes from garbage to expert-level, we've proven the pattern."

## Why This Is Better Than The Original Design

**Original:** Can an architect recognize Bleu's thinking?
**Problem:** Passive observation, might just see "cool AI"

**New:** Can Dustin externalize HIS thinking and see it come back?
**Why better:**
- Active participation (he does the work)
- His own domain (proves generalization)
- Concrete comparison (before/after)
- The "aha" comes from seeing his OWN mind in the output

## Notes

The prompt needs to come from Dustin. We don't know driver development well enough to design a good test case. He picks something real that he knows the answer to.

The externalization process is the key moment. We should capture how that dialogue goes - it's the method in action.
