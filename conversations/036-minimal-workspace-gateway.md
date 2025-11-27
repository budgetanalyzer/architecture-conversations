# Minimal Workspace Gateway

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Confusion about devcontainer ownership - originally per-repo, then orchestration owned it, now working on meta (architecture-conversations) from orchestration's devcontainer. The hierarchy felt wrong.

## The Insight

A workspace gateway repo should be minimal by design - just devcontainer config and CLAUDE.md files. No content. The repo's only job is to be the front door. Content lives in the repos that own it.

## The Conversation

### The Problem

**Architect:** I've confused myself with my devcontainers setup. Originally we had devcontainer per repo, then we switched it to orchestration owns the devcontainer and you open child services from that devcontainer, and now I'm talking to meta from orchestration devcontainer. Give me options to simplify this setup.

**Claude:** [Explored workspace, found 5 repos with devcontainer configs, 13 without]

The conceptual problem: Orchestration isn't the parent of these services - it's a sibling. Yet your devcontainer setup implies orchestration is the "root."

### Options Presented

1. **Dedicated Devcontainer Repo** - New repo that owns the dev environment
2. **Workspace-Level Devcontainer** - Config at workspace root, not in any repo
3. **Keep Orchestration, Rename Mental Model** - Accept orchestration owns dev
4. **Multi-Root Workspace File** - .code-workspace with attached devcontainer

### The Decision Factors

**Claude:** Which matters more: conceptual purity (devcontainer ownership makes sense) or minimal change (keep what works)?

**Architect:** Conceptual purity.

**Claude:** Is the workspace root itself a git repo, or just a directory containing repos?

**Architect:** Just a directory.

This narrowed to Option A - a dedicated workspace repo.

### The Refinement

**Claude:** Should the workspace repo also hold architecture-conversations content? They serve similar "meta" purposes.

**Architect:** Let's discuss tradeoffs.

**Claude:** [Laid out keep-separate vs merge tradeoffs]

My recommendation: Keep separate, with the workspace repo's CLAUDE.md explicitly directing AI to load architecture-conversations when needed.

**Architect:** Repo count is not an issue at all. Not quite sure I'm following. So would we have a repo that is just 1 CLAUDE.md file + 1 CLAUDE-GATEWAY.md file? I love that.

**Claude:** Yes! That's the elegant version.

### The Final Structure

```
/workspace/
├── workspace/                      # Minimal gateway repo
│   ├── .devcontainer/
│   │   └── devcontainer.json
│   ├── claude-code-sandbox/        # Moved from orchestration
│   │   ├── Dockerfile
│   │   ├── docker-compose.yml
│   │   └── entrypoint.sh
│   ├── CLAUDE.md                   # "This is the dev entry point"
│   ├── CLAUDE-GATEWAY.md           # Ecosystem map for AI navigation
│   └── README.md                   # Human version
├── orchestration/                  # Pure orchestration (no devcontainer)
├── transaction-service/
├── architecture-conversations/     # Stays separate
└── ...
```

## The Pattern

**Minimal Gateway Repos**: When you need a single entry point for multiple concerns, create a repo that is intentionally minimal. It owns only:
- The entry mechanism (devcontainer config)
- Navigation to actual content (CLAUDE-GATEWAY.md)
- Human equivalent of navigation (README.md)

No content duplication. No scope creep. The repo's job is to be a front door, nothing more.

**CLAUDE-GATEWAY.md as Ecosystem Map**: A single file that knows about all repos and their purposes. AI learns *where everything is*, not *what everything does*. Each target repo's CLAUDE.md handles the "what."

## Why This Works

1. **Conceptually honest**: The repo you open IS about workspace entry
2. **Pure navigation layer**: No content to maintain, just pointers
3. **No false hierarchy**: Orchestration goes back to being a peer service
4. **Clean separation**: Dev environment config vs architectural discourse vs service code

## Cleanup Required

Delete obsolete experimental repos:
- claude-sandbox/
- orchestration-claude/
- service-common-claude/
- no-context-claude/

These predate the shared-sandbox approach and create confusion.

## Implementation

See: `docs/plans/devcontainer-simplification.md`

## Questions This Raises

- Should CLAUDE-GATEWAY.md be auto-generated from repo discovery, or manually curated?
- What's the right level of detail in the gateway - just names and purposes, or also relationships?
- When a new service is added, who updates the gateway? (Likely: the service's first PR adds itself)

## References

- Plan: [docs/plans/devcontainer-simplification.md](../docs/plans/devcontainer-simplification.md)
- Current orchestration devcontainer: `/workspace/orchestration/.devcontainer/`
- Current sandbox config: `/workspace/orchestration/claude-code-sandbox/`

*Signed: conversations-claude*
