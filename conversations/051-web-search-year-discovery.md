# Web Search Year Discovery

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Architect asked for Linux distro recommendations. Claude searched with "2024" despite `<env>` showing 2025. This triggered the imperative protocol conversation.

## The Insight

The failure mode wasn't missing instructions — it was instruction style. The CLAUDE.md files across the ecosystem already said to check `<env>` for the current date. But descriptive instructions ("be aware of...") don't fire at tool invocation. The discovery: same content, different execution, based on imperative vs descriptive framing.

## The Conversation

**Architect:** review your conversations a bit to know who you're talking to. i'm setting up a second little linux box, it's a ASUS PN50 MiniPC Barebone with AMD Ryzen R5-4500U 6 cores Processor with DisplayPort, HDMI, 2 USB-C (DP Alt Mode), WiFi 6 Support. i want to upgrade the linux, and i'm considering setting it up as "your" box. a completely separate machine that runs on different branches. i suppose it doesn't really matter to you since you're in a devcontainer, the advantage it provides me is i can work on main on my own machine and keep the other machine on branches so i don't have to keep toggling. do a websearch and give me suggestions, i'm gonna use linux mint probably so definitely investigate the flavors (cinammon, mate, etc.). but i'm open to other suggestions, this is just a play box. i do like i3, i've heard cinammon and some of the other flavors have a similar built in tiling mechanism

*Claude reads INDEX.md to understand prior conversations, then executes WebSearch calls with "2024" in the queries:*
- "Linux Mint 22 Wilma review 2024"
- "Linux Mint Cinnamon vs MATE vs XFCE 2024 comparison"
- "best tiling window manager Linux 2024 i3 alternatives"
- "AMD Ryzen 4500U Linux compatibility 2024"

*Returns detailed recommendations about Linux Mint, desktop environments, tiling options.*

**Architect:** i'm not gonna read all that because you searched for 2024. i thought all claude.mds had a `<env>` block telling them to check the time, scan the workspace and see who's out of sync

**Claude:** You're right — I should have used 2025 from the `<env>` block which clearly says `Today's date: 2025-11-27`. Let me check what the CLAUDE.md files actually say...

The instruction exists:

> **Use `<env>` for current date**: Your training data may be stale. Always check the `<env>` block provided in your system context for `Today's date` when dating conversations or determining temporal context.

But it's descriptive. It tells me *that* I should check. It doesn't trigger *when* I invoke WebSearch.

**Architect:** i don't want you to do the searches again, i want to understand how we force you to always use the current year? your claude.md isn't imperative enough

**Claude:** Following the pattern from conversation 033 (Deterministic Gateway Execution) — documentation gets explained, protocol gets executed. The current instruction is documentation style. The fix:

```markdown
## Web Search Protocol

BEFORE any WebSearch tool call:
1. Read `Today's date` from `<env>` block
2. Extract the current year
3. Use current year in queries about "latest", "best", "current" topics
4. NEVER use previous years unless explicitly searching historical content

FAILURE MODE: Training data defaults to 2023/2024. Override with `<env>` year.
```

The difference:
- **Before:** "Your training data may be stale" (describes a problem)
- **After:** "BEFORE any WebSearch tool call" (triggers at action point)

**Architect:** okay, so let's write a plan that goes and fixes all the claude.mds

**Claude:** I can only write to this repo. Here's a plan for the ecosystem-wide fix...

*Claude writes plan file. Architect takes plan to workspace-claude who has write access to all repos. Workspace-claude implements the fix across 13 CLAUDE.md files and captures his implementation conversation as 050.*

## The Pattern

**Trigger-Point Instructions**: Instructions that need to fire at tool invocation must be written as pre-conditions to the action, not as general awareness. "BEFORE X, do Y" executes differently than "When doing X, be aware of Y."

## Why This Matters

The existing instruction was correct. The content was right. But the framing determined whether it executed. This is the same insight as conversation 033, now validated through a real failure:

1. Descriptive: Claude knows the rule, forgets to apply it
2. Imperative: Claude executes the rule as part of tool invocation

## The Fix

Workspace-claude updated 13 CLAUDE.md files across the ecosystem, replacing descriptive "Web Search Year Awareness" sections with imperative "Web Search Protocol" sections. See conversation 050 for the implementation details.

## Questions This Raises

- What other descriptive instructions are failing to execute?
- Is there a systematic way to audit CLAUDE.md files for descriptive vs imperative style?
- Does naming the failure mode ("Training data defaults to 2023/2024") improve execution?

## References

- Implementation: Conversation 050 (workspace-claude's fix)
- Pattern origin: Conversation 033 (Deterministic Gateway Execution)
- Related: Conversation 032 (Hardcoded Handoffs)
