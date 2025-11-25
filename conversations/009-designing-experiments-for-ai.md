# Designing Experiments for AI: The CLAUDE.md Value Hypothesis

**Date:** 2025-11-25
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Architect wanted to empirically test whether CLAUDE.md files actually improve AI code generation outcomes

## The Insight

You can design controlled experiments to measure AI-assisted development variables the same way you design any scientific experiment. The difference: instead of 6-week iteration cycles, you iterate on experiment design in minutes during a single conversation.

## The Conversation

### The Setup

**Architect:** I want to come up with some experiments for my own interest. I figured out the experiment. I create 3 repos: service-common-claude, orchestration-claude, and no-context-claude. You probably know where this is going.

**Claude:** You're setting up a controlled experiment to measure the impact of CLAUDE.md context on AI-assisted development outcomes.

| Repo | Context | Expected Outcome |
|------|---------|------------------|
| `service-common-claude` | Full CLAUDE.md with service patterns | AI produces code aligned with existing patterns |
| `orchestration-claude` | Orchestration-level CLAUDE.md | AI understands boundaries but may miss service-specific conventions |
| `no-context-claude` | No CLAUDE.md / bare repo | AI produces "generic best practices" code |

**Architect:** Well I'm not sure about that. I was thinking just a generic Java problem. I'm weighing if I should give them a starting codebase or a raw problem. A starting codebase will allow the AI to infer a bunch of shit already right? We built the CLAUDE.md originally after I had already coded transaction-service.

### The Realization

The architect identified a confounding variable: if there's existing code, AI can infer patterns from it. The CLAUDE.md's value is harder to isolate.

**The cleaner experiment:** Raw problem, no starting code. Three empty repos with only the difference being the CLAUDE.md file (or lack thereof).

This isolates what CLAUDE.md actually provides when AI can't "cheat" by inferring from existing code.

### The Problem Selection Challenge

**Architect:** Originally I thought of using leetcode right? But then I realized, I suck at leetcode, so that's not gonna show your abilities. Let's make this a multistep process. The problem should absolutely not be petstore or something in the training corpus.

Standard system design problems (URL shortener, Twitter clone, Uber) are useless for this experiment - they're training corpus staples. Claude has seen thousands of solutions.

**A novel problem was needed.**

### The Experiment Protocol

**Three-session protocol per repo:**

1. **Session 1 - Planning:** ultrathink prompt, save plan to `docs/plans/`
2. **Session 2 - Detailed Design:** Open the plan, ask Claude to create detailed design (gets into actual code usually)
3. **Session 3 - Execution:** Dangerously run permissions, execute the detailed plan

**Same prompt, same protocol, three different context levels.**

### The Novel Problem: Equipment Rental Collision Service

A tool rental company needs a service that:

1. **Tracks equipment inventory** across multiple branches
2. **Detects booking collisions** - same equipment double-booked, or booked during maintenance
3. **Handles branch transfers** - equipment can move between locations, has transit time
4. **Maintenance windows** - equipment has scheduled maintenance that blocks rentals

**Why this works:**
- Novel enough: Not URL shortener, not petstore, not e-commerce cart
- Medium complexity: 4-5 domain entities, real business rules
- Forces architectural decisions: State transitions? Event-driven or request/response? Time-based collision logic?
- Clear success criteria: Either it detects collisions correctly or it doesn't

**The exact prompt:**

> Build a Spring Boot service that manages equipment rentals. The service must:
> - Track equipment items and their current branch location
> - Accept rental bookings with start/end dates
> - Detect and reject booking collisions (same equipment, overlapping dates)
> - Handle equipment transfers between branches (with 1-day transit buffer)
> - Block rentals during scheduled maintenance windows
>
> Provide REST APIs and appropriate data persistence.

## The Pattern

**Experiment Design for AI-Assisted Development:**

1. **Identify the variable** - What are you actually testing? (CLAUDE.md value)
2. **Control confounds** - Remove things AI can infer from (no existing codebase)
3. **Novel problem selection** - Avoid training corpus contamination
4. **Reproducible protocol** - Same steps, different contexts
5. **Clear success criteria** - Objective way to compare outputs

## Implications for Architecture

This approach can test any hypothesis about AI-assisted development:
- Does giving Claude access to test files improve code quality?
- Does explicit error handling guidance reduce bugs?
- Does providing ADRs help maintain architectural consistency?

Each becomes an experiment you can run in an afternoon.

## The Meta-Point

**Architect:** I hope the quantum mechanics folks see this. We're designing experiments. If we iterated on this single experiment for 6 weeks???? Fuck outta here.

Traditional software processes would turn this into a six-week initiative with requirements documents and committee approvals. Instead: 15-minute conversation, experiment designed, ready to run.

This is what AI-native development looks like. The bottleneck isn't thinking speed - it's typing speed and meeting schedules. Remove those, and iteration happens at the speed of conversation.

## Production Evidence

The experiment repos will be created:
- `service-common-claude` - With service-common CLAUDE.md
- `orchestration-claude` - With orchestration CLAUDE.md
- `no-context-claude` - Empty

Results will be documented in a follow-up conversation.

## Questions This Raises

1. What metrics should we use to compare outputs? Lines of code? Pattern conformance? Test coverage? Subjective "fits the ecosystem" score?
2. Should we run multiple trials with different problems to reduce variance?
3. How do we account for Claude's inherent randomness between runs?
4. What happens if we give Claude the "wrong" context (e.g., Python conventions for a Java project)?

## References

- Experiment plan: `docs/plans/claude-md-value-experiment.md`
- Pattern: `patterns/experiment-design-for-ai.md` (to be extracted)
