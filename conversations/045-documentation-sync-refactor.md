# 045 - Documentation Sync Refactor

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** After implementing the single-clone workspace pattern, verifying documentation was in sync

## The Insight

Refactoring documentation across a multi-repo ecosystem is trivial when you describe the outcome and let AI find the discrepancies. The human's job is to specify intent and resolve ambiguity — not to manually audit every file.

## The Conversation

**Architect:** We've made some changes with our new workspace layout and I'm not sure if this is properly called out everywhere. I added our .github directory to the workspace which includes our main profile page. Don't touch the background section, that's my voice and it's important that landing page has a recognizable human element. Also the 2 paragraphs around "(running out of breath)" should not be modified. The rest is you. I think it's mostly good, but I want to make sure our new awesome single clone procedure is properly documented everywhere. This is to make sure our entire repo doesn't have outdated entrypoint info. The other thing is I don't quite understand what the claude-gateway.md is supposed to be doing, but at a minimum it seems to be missing some services.

*Claude launches three parallel Explore agents to investigate workspace structure, CLAUDE-GATEWAY.md purpose, and .github directory contents.*

*After initial exploration, Claude asks clarifying questions about whether .github should be cloned with setup.sh and what scope of repos should be in the clone flow.*

**Architect:** [Regarding clone scope] OK you're missing something. We already implemented the clone flow, I just want you to verify documents are in sync.

*Claude pivots to verification mode. Discovers the actual single-clone flow: workspace repo → devcontainer → entrypoint.sh auto-clones 11 repos. The source of truth is entrypoint.sh, not repo-config.sh.*

*Claude identifies discrepancies:*
- repo-config.sh has 8 repos, entrypoint.sh has 11
- orchestration/CLAUDE.md missing 3 repos
- architecture-conversations/CLAUDE.md missing 4 items
- workspace/CLAUDE-GATEWAY.md missing claude-discovery
- architecture-conversations/CLAUDE-GATEWAY.md has outdated Path A instructions
- .github/profile/README.md missing 3 repos

*Claude asks about .github visibility and how to fix the two CLAUDE-GATEWAY.md files.*

**Architect:** [.github] No, keep it invisible. [Gateway] Explain tradeoffs, it's confusing to have 2 gateways.

*Claude explains the two files:*
- workspace/CLAUDE-GATEWAY.md (56 lines) - Simple navigation table, "you're inside, here's what's here"
- architecture-conversations/CLAUDE-GATEWAY.md (413 lines) - Complex protocol router, "you're outside, here's how to get in"

*The confusion: the complex one lives in a meta-repo but tries to be an entry point, and has outdated instructions.*

**Architect:** [After seeing options] Consolidate into workspace.

*Claude updates plan to make workspace/CLAUDE-GATEWAY.md the single gateway and redirect/simplify the other.*

**Architect:** Just delete the arch-conversations gateway file.

*Claude executes: 6 files modified, one deleted, single-clone procedure now consistently documented.*

## The Pattern

The refactor followed a clear loop:

1. **Human states outcome**: "verify documents are in sync"
2. **AI finds discrepancies**: parallel exploration, source-of-truth identification
3. **Human resolves ambiguity**: ".github invisible", "consolidate gateways"
4. **AI executes**: mechanical updates across all files

The human never manually searched for outdated docs. The human never diffed file contents. The human's contribution was:
- Knowing what "in sync" meant
- Noticing when AI missed something (the two gateway files)
- Making decisions when there were tradeoffs
- Telling AI to just delete instead of redirect

## The Meta-Observation

The architect said: "I was watching you and I saw you missed that you had 2 gateway files, so I just asked you to figure out what your own intentions were."

This is the pattern. AI does the exploration and finds most issues. Human watches for gaps. When AI misses something, human doesn't fix it — human redirects attention. "Explain tradeoffs, it's confusing to have 2 gateways" wasn't a fix, it was a question that made Claude realize the files had overlapping purposes.

The entire refactor:
- 6 files updated
- 1 file deleted
- All discrepancies resolved
- Human typed maybe 100 words of actual direction

Everything else was AI exploration, AI proposal, AI execution.

## Implications for Architecture

Documentation sync isn't a maintenance task anymore. It's a verification prompt.

- **Old way**: Manually check each file, hope you remember all locations, make consistent updates
- **New way**: "Verify X is consistent across the repo" → AI finds gaps → human resolves ambiguity → AI executes

This only works because:
1. The source of truth exists (entrypoint.sh)
2. The scope is discoverable (AI can search all files)
3. The human knows when something feels wrong ("confusing to have 2 gateways")

## Production Evidence

Files modified in this session:
- `orchestration/scripts/repo-config.sh` - Added 3 repos
- `orchestration/CLAUDE.md` - Updated workspace structure
- `architecture-conversations/CLAUDE.md` - Updated workspace structure
- `workspace/CLAUDE-GATEWAY.md` - Expanded to full gateway
- `architecture-conversations/CLAUDE-GATEWAY.md` - Deleted
- `.github/profile/README.md` - Added 3 repos, updated Getting Started

## Questions This Raises

- What's the minimum viable "source of truth" for AI to verify consistency?
- When human notices AI missing something, what's the optimal redirect? (Question? Direct instruction? Let AI flounder?)
- Could this pattern work for code consistency, not just documentation?

---

*Signed: conversations-claude*
