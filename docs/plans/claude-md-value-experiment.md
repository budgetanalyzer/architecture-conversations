# Experiment: Measuring CLAUDE.md Value in Greenfield Development

## Hypothesis

Explicit architectural context via CLAUDE.md produces meaningfully different (better aligned) code than AI's generic training when there's no existing code to pattern-match against.

## Experimental Design

### Independent Variable
The CLAUDE.md content provided:

| Repo | CLAUDE.md Source | What It Provides |
|------|------------------|------------------|
| `service-common-claude` | service-common's CLAUDE.md | Explicit patterns, conventions, package structure |
| `orchestration-claude` | orchestration's CLAUDE.md | High-level architecture, service boundaries |
| `no-context-claude` | None | Just the problem statement |

### Controls
- **No existing code** - Empty repos (except markdown docs), AI can't infer from codebase
- **Same prompt** - Identical problem statement to all three
- **Same protocol** - Session-per-unit workflow for each (see below)
- **Novel problem** - Not in training corpus
- **Full markdown structure** - Copy all `.md` files from parent repos, not just CLAUDE.md. The just-in-time navigable documentation structure is part of what's being tested.

### The Problem

```
Build a Spring Boot service that manages equipment rentals. The service must:
- Track equipment items and their current branch location
- Accept rental bookings with start/end dates
- Detect and reject booking collisions (same equipment, overlapping dates)
- Handle equipment transfers between branches (with 1-day transit buffer)
- Block rentals during scheduled maintenance windows

Provide REST APIs and appropriate data persistence.
```

### Protocol (Per Repo)

**Session 1: Planning (ultrathink/Opus)**
- Fresh Claude session
- `ultrathink` mode (Opus)
- Prompt: The problem statement above
- Output: Save plan to `docs/plans/equipment-rental-plan.md`
- Plan should specify phases and steps
- Exit session

**Session 2: Detailed Design**
- Fresh Claude session
- Open `docs/plans/equipment-rental-plan.md`
- Prompt: "Create a detailed implementation plan with actual code structure. Break down into executable units - each unit should be completable in a single session without running out of context."
- Output: Detailed design with phases/steps, class names, method signatures
- **The granularity of this breakdown is part of what's being tested**
- Exit session

**Session 3+: Execution (One Session Per Unit)**
- Fresh Claude session per step/phase in the detailed plan
- Dangerous permissions enabled
- Prompt: "Execute [Phase X / Step Y] from the plan in docs/plans/"
- Exit session after completing that unit
- Repeat for each unit in the plan

**Why session-per-unit?**
Context exhaustion causes degraded output. By forcing one session per unit of work:
1. Tests how well Claude scopes work into context-appropriate chunks
2. Simulates real-world usage patterns (architects don't run 100k token sessions)
3. Makes plan granularity a measurable variable

**Prediction:** `service-common-claude` will produce better-scoped units because the context guides appropriate decomposition.

## Metrics to Capture

### Quantitative
- Lines of code generated
- Number of files created
- Test coverage (if tests generated)
- Build success (does it compile?)
- Collision detection accuracy (manual test cases)
- **Number of execution sessions required** (measures plan granularity)
- **Session count vs. successful completion** (did the breakdown work?)

### Qualitative
- Package structure choices
- Naming conventions used
- Error handling patterns
- API design style
- Does it "feel like" Budget Analyzer code? (subjective)

## Expected Outcomes

| Repo | Prediction |
|------|------------|
| `service-common-claude` | Matches existing patterns, uses established conventions |
| `orchestration-claude` | Architecturally sound but stylistically divergent |
| `no-context-claude` | Generic Spring Boot tutorial style, functional but alien |

## Execution

**Claude will execute this entire experiment.** Load this plan and create a detailed execution plan in a follow-up session.

### Repo Setup Checklist
- [ ] Create `service-common-claude` repo with all `.md` files from service-common
- [ ] Create `orchestration-claude` repo with all `.md` files from orchestration
- [ ] Create `no-context-claude` repo with no markdown context

### Per-Repo Execution
- [ ] Session 1: Planning (ultrathink)
- [ ] Session 2: Detailed design with unit breakdown
- [ ] Sessions 3+: One session per unit until complete

### Analysis
- [ ] Compare outputs across all three repos
- [ ] Document findings in conversation 010

## Follow-up Experiments

If results are significant:
1. **Wrong context test** - Give Python conventions for Java project
2. **Partial context test** - CLAUDE.md with only some sections
3. **Stale context test** - Outdated CLAUDE.md vs current patterns
4. **Multi-service test** - Does orchestration context help when building something that calls multiple services?
