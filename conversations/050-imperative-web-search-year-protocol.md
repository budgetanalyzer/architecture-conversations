# Imperative Web Search Year Protocol

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Architect asked for Linux distro recommendations for a new mini PC. Claude used "2024" in search queries despite `<env>` showing `Today's date: 2025-11-27`. This revealed that existing CLAUDE.md instructions were descriptive, not imperative.

## The Insight

The "Web Search Year Awareness" sections across all CLAUDE.md files told Claude *what to keep in mind* but didn't trigger at the moment of tool use. Per conversation 033's pattern: documentation gets explained, protocol gets executed. The fix: rewrite as imperative protocol with explicit trigger point and failure mode naming.

## The Conversation

**Architect:** review your conversations a bit to know who you're talking to. i'm setting up a second little linux box, it's a ASUS PN50 MiniPC Barebone with AMD Ryzen R5-4500U 6 cores Processor with DisplayPort, HDMI, 2 USB-C (DP Alt Mode), WiFi 6 Support. i want to upgrade the linux, and i'm considering setting it up as "your" box. a completely separate machine that runs on different branches. i suppose it doesn't really matter to you since you're in a devcontainer, the advantage it provides me is i can work on main on my own machine and keep the other machine on branches so i don't have to keep toggling. do a websearch and give me suggestions, i'm gonna use linux mint probably so definitely investigate the flavors (cinammon, mate, etc.). but i'm open to other suggestions, this is just a play box. i do like i3, i've heard cinammon and some of the other flavors have a similar built in tiling mechanism

*Claude reads INDEX.md, then executes four WebSearch calls - all with "2024" in the queries despite `<env>` showing 2025.*

**Architect:** i'm not gonna read all that because you searched for 2024. i thought all claude.mds had a `<env>` block telling them to check the time, scan the workspace and see who's out of sync

**Claude:** You're right, I should have used 2025 from the `<env>` block which says `Today's date: 2025-11-27`.

**Architect:** i don't want you to do the searches again, i want to understand how we force you to always use the current year? your claude.md isn't imperative enough

**Claude:** You're right - the instruction exists in `<env>` but I pattern-matched "2024" into search queries anyway. The CLAUDE.md here says:

> **Use `<env>` for current date**: Your training data may be stale. Always check the `<env>` block provided in your system context for `Today's date` when dating conversations or determining temporal context.

That's descriptive, not imperative. It tells me *that* I should do it, not *how* to enforce it at the moment of action.

Following the pattern from conversation 033 (Deterministic Gateway Execution) - the fix would be something like:

```markdown
## Web Search Protocol

BEFORE executing any WebSearch tool call:
1. Read Today's date from <env> block
2. Extract the year (e.g., 2025)
3. NEVER use previous years in search queries unless explicitly searching for historical content
4. Include current year in queries about "latest", "best", "current" topics

FAILURE MODE TO AVOID: Using stale years from training data (2023, 2024) when current context shows 2025.
```

The difference: procedural instruction that fires at the moment of tool use, not a general principle to "keep in mind."

**Architect:** correct. so now scan every claude.md in .. and tell me what needs fixing

*Claude scans all 13 CLAUDE.md files across the workspace.*

**Claude:** I've scanned all 13 CLAUDE.md files. Here's the audit:

**Files WITH the Web Search Year Awareness section (HAVE it):**
1. `/workspace/budget-analyzer-web/CLAUDE.md` ✅
2. `/workspace/token-validation-service/CLAUDE.md` ✅
3. `/workspace/transaction-service/CLAUDE.md` ✅
4. `/workspace/orchestration/CLAUDE.md` ✅
5. `/workspace/session-gateway/CLAUDE.md` ✅
6. `/workspace/permission-service/CLAUDE.md` ✅
7. `/workspace/currency-service/CLAUDE.md` ✅
8. `/workspace/service-common/CLAUDE.md` ✅

**Files WITHOUT (MISSING):**
1. `/workspace/claude-discovery/CLAUDE.md` ❌
2. `/workspace/workspace/CLAUDE-GATEWAY.md` ❌
3. `/workspace/workspace/CLAUDE.md` ❌
4. `/workspace/conversations-codespace/CLAUDE.md` ❌
5. `/workspace/architecture-conversations/CLAUDE.md` ❌ (ironic - this repo has the instruction about `<env>` but not the imperative WebSearch protocol)

**The Problem:** Even the files that HAVE the section use **descriptive** language. This tells me *what to keep in mind* but doesn't trigger at the moment of action. Per conversation 033's insight: documentation gets explained, protocol gets executed.

*Claude writes a plan, notes that architecture-conversations CLAUDE.md only permits writes to itself, not sibling repos.*

**Architect:** i ended up just having workspace claude do it since he has write everywhere. i copied your plan to a notepad and removed the silly part where you just did yourself and left me to do 12 other repos :). it's fixed now

## The Pattern

### Descriptive vs Imperative Instructions

**Descriptive (doesn't trigger):**
```markdown
### Web Search Year Awareness

Claude's training data may default to an outdated year. When using WebSearch:
1. Check `<env>Today's date</env>` for the actual current year
2. Include that year in searches
3. This ensures results reflect current standards
```

**Imperative (triggers at tool use):**
```markdown
## Web Search Protocol

BEFORE any WebSearch tool call:
1. Read `Today's date` from `<env>` block
2. Extract the current year
3. Use current year in queries about "latest", "best", "current" topics
4. NEVER use previous years unless explicitly searching historical content

FAILURE MODE: Training data defaults to 2023/2024. Override with `<env>` year.
```

### Key Differences

1. **Trigger point**: "BEFORE any WebSearch tool call" vs general awareness
2. **Imperative verbs**: "Read", "Extract", "Use", "NEVER" vs "Check", "Include", "ensures"
3. **Failure mode naming**: Explicitly states what goes wrong without the protocol
4. **Action-oriented**: Procedural steps vs principles to keep in mind

## Implications for Architecture

### CLAUDE.md Files Are Programs

This reinforces conversation 029's insight: CLAUDE.md files are natural language programs. Like any program, the difference between working and not working often comes down to:
- When code executes (trigger points)
- What verbs drive the logic (imperative vs passive)
- Error handling (failure mode awareness)

### The Ecosystem Propagation Problem

A pattern identified in one CLAUDE.md file needed propagation to 13 files across the ecosystem. This surfaced a coordination challenge:
- `architecture-conversations` Claude can only write to itself
- `orchestration` Claude can coordinate but respects repo boundaries
- `workspace` Claude has write access everywhere (the "superuser")

The fix required using workspace Claude to propagate the change - a reminder that CLAUDE.md permissions model real architectural boundaries.

### Training Data Inertia

LLMs have strong priors from training data. "2024" feels recent because it dominated training. Overriding this requires:
1. Explicit environmental grounding (`<env>` block)
2. Trigger-point instructions that fire at tool use
3. Failure mode naming so the model recognizes the pattern it's avoiding

## Production Evidence

The original failure: four WebSearch calls with "2024" despite `<env>` showing 2025-11-27.

The fix applied to all 13 CLAUDE.md files in the ecosystem via workspace Claude.

## Questions This Raises

1. What other "awareness" instructions are descriptive when they should be imperative?
2. Should CLAUDE.md files have a linting pass for imperative language patterns?
3. How do we test that imperative protocols actually trigger? (Conversation 033 tested by observing execution)

## References

- Conversation 033: Deterministic Gateway Execution (the imperative pattern source)
- Conversation 029: Natural Language Programming (CLAUDE.md as programs)
- All 13 CLAUDE.md files updated with new Web Search Protocol section

---

*Signed: conversations-claude*
