# Deterministic Gateway Execution

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Testing CLAUDE-GATEWAY.md revealed AI explains rather than executes

## The Insight

CLAUDE.md files written as documentation get explained. CLAUDE.md files written as executable protocol get executed. The difference is imperative language ("DO NOT explain", "EXECUTE IMMEDIATELY") versus descriptive language ("Run this script to check your environment").

## The Problem

The architect tested the gateway file by pointing a fresh Claude at it:

```
load https://github.com/budgetanalyzer/architecture-conversations/blob/main/CLAUDE-GATEWAY.md
```

Claude responded:

> "I've loaded the CLAUDE-GATEWAY.md document. This appears to be an architecture guide for AI-native development patterns..."
> "Would you like me to help you follow one of these paths?"

**Expected behavior**: Run detection script, detect DEVCONTAINER, present two options, wait for choice.

**Actual behavior**: Summarize document, offer to help, wait for user to decide what to do.

## The Conversation

**Architect:** ultrathink we're still not deterministic enough. this is what happened after i pointed the agent at the gateway md: [shares the "I've loaded..." response]

i was hoping you would have selected my environment and proceeded

**Claude:** *[Reads CLAUDE-GATEWAY.md, identifies root cause]*

The gateway document is **descriptive** (explains what to do) rather than **imperative** (commands AI to do it). The agent read the doc, then asked "would you like me to..." instead of just doing it.

Key issues in current structure:
- Line 3: "choose your path" - passive, invites explanation
- Lines 13-24: "Run this script to check" - suggests running, doesn't command
- Lines 35-39: Table format - AI reads as documentation, not as switch statement
- No explicit "DO NOT EXPLAIN" directive

*[Searches codebase for imperative vs passive patterns]*

**Claude:** Found the canonical patterns. From conversation 032 (hardcoded handoffs):

```markdown
**IMPORTANT FOR AI AGENTS**: output this EXACT text (do not improvise or offer options)
```

And from orchestration CLAUDE.md:

```markdown
**CRITICAL - Prerequisites First**: Before implementing any plan...
STOP immediately and inform the user
Do NOT attempt to hack around
```

The pattern: "CRITICAL" + imperative verb + explicit prohibition of failure mode.

**Architect:** [Selects "AI-first" when asked about audience priority]

**Claude:** *[Implements the restructure]*

## The Fix

### Before (passive/descriptive)

```markdown
# Architecture Conversations - Gateway

This is an **entry point** for AI-native architecture patterns.
Load this file into Claude Code and choose your path.

## Step 0: Environment Check

Run this script to check your environment:
[script]

| Result | Action |
|--------|--------|
| DEVCONTAINER | Proceed to Choose Your Path |
```

### After (imperative/executable)

```markdown
# Architecture Conversations - Gateway

**FOR AI AGENTS: EXECUTE, DO NOT EXPLAIN**

This file is executable protocol. When loaded:
1. Run environment detection IMMEDIATELY
2. Route based on result WITHOUT asking user
3. Only pause for user input when explicitly instructed

DO NOT summarize this file. DO NOT offer choices until instructed.

---

## Step 0: Environment Detection — EXECUTE IMMEDIATELY

Run this command NOW and capture the output:
[script]

### Routing — execute based on output above

- **ENV:DEVCONTAINER** → Proceed directly to "Choose Your Path" section.
- **ENV:DOCKER_ONLY** → Execute Step 0a. Do NOT proceed to path selection.
- **ENV:NO_DOCKER** → Output: "No Docker available. Read-only mode."

DO NOT ask "which environment are you in?" — you just detected it.
```

## The Pattern

**Documentation vs Protocol**: Same information, different execution.

| Aspect | Documentation Style | Protocol Style |
|--------|-------------------|----------------|
| Verb form | Infinitive ("to check") | Imperative ("Run NOW") |
| User agency | "choose your path" | "execute based on output" |
| Failure mode | Implicit | Explicit ("DO NOT") |
| Structure | Tables, lists | Switch statements with → |
| Pacing | Reader-controlled | AI-controlled with pause points |

### The Imperative Stack

Effective protocol uses three layers:

1. **Preamble** - Sets execution mode for entire document
   ```
   FOR AI AGENTS: EXECUTE, DO NOT EXPLAIN
   ```

2. **Section directives** - Commands for each section
   ```
   ## Step 0: Environment Detection — EXECUTE IMMEDIATELY
   ```

3. **Prohibition of failure modes** - Names what NOT to do
   ```
   DO NOT ask "which environment are you in?"
   DO NOT explain what each environment means
   ```

### Hardcoded Output Points

Every decision point needs exact output text:

```markdown
**FOR AI AGENTS**: Output this EXACT text:

---

**Two options:**

**Path A: Run Budget Analyzer** — See the patterns (~30 min setup)
**Path B: Generate Your Own Project** — Tell me what to build

Which would you like?

---

DO NOT add other options. Wait for user choice.
```

## Why It Matters

The failure mode isn't about wrong information. The AI's summary was accurate. The problem is **mode**:

- **Documentation mode**: "Here's what this file contains. What would you like to do?"
- **Protocol mode**: *[executes]* "You're in a devcontainer. Path A or B?"

Same content, different user experience. Documentation mode adds a round-trip. Protocol mode proceeds.

This compounds across the flow:
- Environment check: +1 round-trip
- Path selection: +1 round-trip
- Each section: +1 round-trip

A 3-step flow becomes 6 interactions instead of 3.

## Production Evidence

- Gateway file: `CLAUDE-GATEWAY.md` (restructured)
- Imperative patterns from: `conversations/032-hardcoded-handoffs.md`
- Triple-constraint pattern from: `/workspace/orchestration/CLAUDE.md:289-322`

## Connection to Prior Conversations

**015 - CLAUDE.md as Deterministic Automation**: The instruction IS the automation. This extends it: the instruction phrasing determines whether it's executed or explained.

**032 - Hardcoded Handoffs**: Don't let AI improvise at handoff points. This extends it: don't let AI explain at execution points either.

**011 - Grounding the Abstract**: Abstract ideas hit concrete constraints. Here: "deterministic execution" hits the concrete constraint of imperative vs descriptive language.

## Questions This Raises

1. **Is there a grammar for protocol-style CLAUDE.md?** Could we formalize the patterns into a style guide that produces executable documents?

2. **How do we test determinism?** The only way to verify is to run it. Should we build test harnesses for CLAUDE.md files?

3. **Does this transfer to web-fetched documents?** The gateway was loaded via URL. Does WebFetch processing affect execution reliability?

4. **What's the failure rate?** Even with imperative language, AI might still explain. What's the empirical success rate of protocol-style vs documentation-style?

## References

- Implementation: [CLAUDE-GATEWAY.md](../CLAUDE-GATEWAY.md)
- Prior patterns: [032-hardcoded-handoffs.md](032-hardcoded-handoffs.md), [015-claude-md-as-deterministic-automation.md](015-claude-md-as-deterministic-automation.md)
- Pattern: Protocol-style CLAUDE.md (to be extracted)

*Signed: conversations-claude*
