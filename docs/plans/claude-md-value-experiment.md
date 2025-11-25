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
- **No existing code** - Empty repos, AI can't infer from codebase
- **Same prompt** - Identical problem statement to all three
- **Same protocol** - Three-session workflow for each
- **Novel problem** - Not in training corpus

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

**Session 1: Planning**
- Fresh Claude session
- `ultrathink` mode
- Prompt: The problem statement above
- Output: Save plan to `docs/plans/equipment-rental-plan.md`
- Exit session

**Session 2: Detailed Design**
- Fresh Claude session
- Open `docs/plans/equipment-rental-plan.md`
- Prompt: "Create a detailed implementation plan with actual code structure"
- Output: Detailed design with class names, method signatures, etc.
- Exit session

**Session 3: Execution**
- Fresh Claude session
- Dangerous permissions enabled
- Prompt: "Execute the plan in docs/plans/"
- Output: Working code

## Metrics to Capture

### Quantitative
- Lines of code generated
- Number of files created
- Test coverage (if tests generated)
- Build success (does it compile?)
- Collision detection accuracy (manual test cases)

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

## Execution Checklist

- [ ] Create `service-common-claude` repo with CLAUDE.md from service-common
- [ ] Create `orchestration-claude` repo with CLAUDE.md from orchestration
- [ ] Create `no-context-claude` repo with no CLAUDE.md
- [ ] Run Session 1 on all three
- [ ] Run Session 2 on all three
- [ ] Run Session 3 on all three
- [ ] Compare outputs
- [ ] Document findings in conversation

## Follow-up Experiments

If results are significant:
1. **Wrong context test** - Give Python conventions for Java project
2. **Partial context test** - CLAUDE.md with only some sections
3. **Stale context test** - Outdated CLAUDE.md vs current patterns
4. **Multi-service test** - Does orchestration context help when building something that calls multiple services?
