# Imperative Web Search Year Protocol

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Following conversation 033's insight that documentation gets explained while protocol gets executed

## The Insight

The existing "Web Search Year Awareness" sections across all CLAUDE.md files were descriptive — they explained what to keep in mind. Per conversation 033: documentation gets explained, protocol gets executed. The fix: replace descriptive text with imperative protocol that triggers at the moment of WebSearch tool use.

## The Problem

Claude's training data defaults to stale years (2023, 2024) when constructing WebSearch queries. The `<env>` block provides `Today's date` but the existing instruction was:

```markdown
### Web Search Year Awareness

Claude's training data may default to an outdated year. When using WebSearch for best practices or current information:

1. Check `<env>Today's date</env>` for the actual current year
2. Include that year in searches (e.g., "Spring Boot best practices 2025" not 2024)
3. This ensures results reflect current standards, not outdated patterns
```

This is **descriptive** — it tells Claude what to be aware of. It doesn't fire at the moment of action.

## The Conversation

**Architect:** *[Shares plan for imperative web search year protocol]*

The plan identified:
- 9 repos with existing descriptive section (replace)
- 4 repos missing the section entirely (add)

New protocol text:

```markdown
## Web Search Protocol

BEFORE any WebSearch tool call:
1. Read `Today's date` from `<env>` block
2. Extract the current year
3. Use current year in queries about "latest", "best", "current" topics
4. NEVER use previous years unless explicitly searching historical content

FAILURE MODE: Training data defaults to 2023/2024. Override with `<env>` year.
```

**Claude:** *[Implements across all 13 files]*

Files updated with replacement:
1. `/workspace/architecture-conversations/CLAUDE.md`
2. `/workspace/budget-analyzer-web/CLAUDE.md`
3. `/workspace/token-validation-service/CLAUDE.md`
4. `/workspace/transaction-service/CLAUDE.md`
5. `/workspace/orchestration/CLAUDE.md`
6. `/workspace/session-gateway/CLAUDE.md`
7. `/workspace/permission-service/CLAUDE.md`
8. `/workspace/currency-service/CLAUDE.md`
9. `/workspace/service-common/CLAUDE.md`

Files with new addition:
10. `/workspace/claude-discovery/CLAUDE.md`
11. `/workspace/workspace/CLAUDE-GATEWAY.md`
12. `/workspace/workspace/CLAUDE.md`
13. `/workspace/conversations-codespace/CLAUDE.md`

**Architect:** can you commit all those so i just have to push?

**Claude:** *[Creates 12 commits across all repos, ready for push]*

## The Pattern

**Descriptive vs Imperative Protocol**

| Aspect | Descriptive (Before) | Imperative (After) |
|--------|---------------------|-------------------|
| Trigger | Background awareness | "BEFORE any WebSearch tool call" |
| Phrasing | "may default", "keep in mind" | "Read", "Extract", "Use", "NEVER" |
| Failure mode | Implied | Explicit: "Training data defaults to 2023/2024" |
| Action | Suggestions | Commands |

### Why "BEFORE" Matters

The key structural change is positioning:

```markdown
BEFORE any WebSearch tool call:
```

This creates a **trigger point** — the protocol activates at the moment of tool use, not as general background information to remember.

### The FAILURE MODE Pattern

From conversation 033, effective protocol names what NOT to do. Here:

```markdown
FAILURE MODE: Training data defaults to 2023/2024. Override with `<env>` year.
```

This:
1. Names the specific failure (using 2023/2024)
2. Provides the override mechanism (read from `<env>`)
3. Is concise enough to be actionable

## Why It Matters

Search queries with stale years return stale results. A query for "React best practices 2024" in late 2025 misses an entire year of ecosystem evolution.

The compound effect:
- Outdated library recommendations
- Deprecated patterns presented as current
- Security advisories missed
- Framework version mismatches

The fix is trivial — use the right year. The challenge is getting the AI to do it reliably. Protocol-style instructions fire at the moment of action.

## Production Evidence

- 9 files with `### Web Search Year Awareness` replaced with `## Web Search Protocol`
- 4 files with section added
- 12 commits across ecosystem repos

## Connection to Prior Conversations

**033 - Deterministic Gateway Execution**: The direct predecessor. Established that documentation gets explained, protocol gets executed. This conversation applies that insight to a specific failure mode (stale search years).

**015 - CLAUDE.md as Deterministic Automation**: The instruction IS the automation. Here: the protocol IS the year correction mechanism.

**032 - Hardcoded Handoffs**: Don't let AI improvise at critical points. Here: don't let AI default to training-data years at search time.

## Questions This Raises

1. **What other training-data defaults cause problems?** The year issue is visible in search. What silent defaults affect code generation, library choices, API patterns?

2. **Can we measure protocol effectiveness?** With the imperative protocol, does Claude actually use 2025 in searches? What's the before/after accuracy rate?

3. **Should CLAUDE.md have a "protocols" section?** A standardized location for imperative instructions that trigger at tool-use time?

## References

- Implementation: All 13 CLAUDE.md files across budgetanalyzer ecosystem
- Pattern source: [033-deterministic-gateway-execution.md](033-deterministic-gateway-execution.md)
- Pattern: Imperative Protocol (trigger-point instructions vs background awareness)

*Signed: conversations-claude*
