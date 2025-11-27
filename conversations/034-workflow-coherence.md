# Workflow Coherence: When Instructions Contradict Themselves

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Debugging why AI executed `docker compose up` when it should have stopped and shown options

## The Insight

Instructions that mix incompatible workflows create unpredictable AI behavior. The AI follows what's written — if Step 6 says "run docker compose," it runs docker compose, even when Step 10's handoff text assumes VS Code devcontainer flow. Coherence isn't about any single instruction being wrong; it's about the entire document describing one workflow.

## The Conversation

**Architect:** [Shows debug log where AI created files then ran `docker compose up -d --build`]

ultrathink we're debugging the claude-gateway script and this is what happened. you're homey tried to proceed when he should have stopped and showed options. it's still executing, but it's trying to start docker.

**Claude:** I see the problem. Looking at CLAUDE-GATEWAY.md lines 31-32:

> **ENV:DOCKER_ONLY** → Execute Step 0a (create sandbox). After completion, output the EXACT handoff text specified in Step 0a. Do NOT proceed to path selection.

But the agent followed the Step 0a instructions literally ("Execute ALL commands in this section sequentially") and ran `docker compose up -d --build` (step 6) instead of stopping after file creation to output the handoff text.

The document has a conflicting design:

1. **Step 0a says** "Execute ALL commands" including `docker compose up -d --build`
2. **The handoff text says** "Exit this VS Code window" and "Click 'Reopen in Container'" — which assumes VS Code devcontainer flow
3. **But there's no `.devcontainer/` directory** being created, so "Reopen in Container" won't work

The root cause: The document conflates two different workflows:
- **Standalone Docker workflow**: Build with `docker compose up`, exec into container
- **VS Code Devcontainer workflow**: Create `.devcontainer/`, use "Reopen in Container"

The handoff text describes the VS Code flow, but the files created are for standalone Docker.

**Architect:** [Chooses VS Code Devcontainer as intended workflow]

**Claude:** Got it. VS Code Devcontainer flow. That means:

**The bug**: Step 0a creates `docker-compose.yml` in `sandbox/` but no `.devcontainer/` config. Then tells the AI to run `docker compose up`. But the handoff text says "Click Reopen in Container" which requires `.devcontainer/devcontainer.json`.

**The fix**:
1. Create `.devcontainer/` directory with `devcontainer.json`
2. Remove or modify the `docker compose up` step (VS Code handles building)
3. The AI should stop after creating files and show the handoff text

**Architect:** [Chooses `updateRemoteUserUID` for permissions handling]

**Claude:** Simpler. No .env needed — VS Code's `updateRemoteUserUID` handles permissions automatically.

[Implements changes: rewrites Step 0a to create only `.devcontainer/devcontainer.json` and simplified `Dockerfile`, removes all docker commands, updates routing instruction to say "create sandbox files only - do NOT run docker commands"]

## The Pattern

**Workflow Coherence**: Every executable CLAUDE.md should describe exactly one workflow from start to finish. If the instructions create standalone Docker files but the handoff assumes devcontainer, the AI will execute what's written (standalone Docker) while the human expects what's implied (devcontainer).

**Detection**: When debugging AI behavior, trace the complete execution path. The bug isn't where it failed — it's where the document first assumes a different workflow than it specifies.

**Fix**: Choose one workflow. Remove all traces of alternatives. The document should be impossible to execute wrong, not just difficult.

## The Specific Fix

**Before (incoherent):**
```
Step 0a: Create a Sandbox — EXECUTE ALL STEPS
  1. mkdir sandbox/
  2. Create .env with USER_UID
  3. Create Dockerfile (with ARG USER_UID)
  4. Create docker-compose.yml
  5. Create entrypoint.sh
  6. docker compose up -d --build    ← Standalone Docker
  7. docker compose exec bash

Handoff text:
  "Click Reopen in Container"       ← VS Code Devcontainer
```

**After (coherent):**
```
Step 0a: Create a Sandbox — EXECUTE FILE CREATION ONLY
  1. mkdir .devcontainer/
  2. Create devcontainer.json (with updateRemoteUserUID: true)
  3. Create Dockerfile (simplified, no USER_UID args)
  [STOP — no docker commands]

Handoff text:
  "Click Reopen in Container"       ← Matches what was created
```

## Implications for Architecture

1. **Test the complete path**: Load the gateway, trigger ENV:DOCKER_ONLY, verify the AI stops at the right point
2. **One workflow per document**: If you need multiple workflows, branch early (separate sections) rather than interleaving
3. **Instructions vs implications**: AI executes instructions. Humans infer implications. When they diverge, AI "fails" by doing exactly what was written

## Production Evidence

The fix reduced Step 0a from 8 steps + 5 files to 3 steps + 2 files:
- Removed: .env, docker-compose.yml, entrypoint.sh
- Removed: All `docker compose` commands
- Added: devcontainer.json with `updateRemoteUserUID: true`
- Simplified: Dockerfile (no build args)

## Questions This Raises

1. **Testing executable CLAUDE.md**: How do you CI/CD test that a gateway file produces the expected AI behavior?
2. **Workflow detection**: Could AI detect workflow incoherence before executing? ("This document mixes standalone Docker and devcontainer patterns — which do you want?")
3. **Version control for AI behavior**: When the fix ships, existing sessions may have cached the old instructions. How do you invalidate AI "memory" of stale patterns?

## References

- Implementation: `CLAUDE-GATEWAY.md` Step 0a section
- Prior conversation: [033-deterministic-gateway-execution.md](033-deterministic-gateway-execution.md)
- Pattern: Workflow coherence (this conversation)

*Signed: conversations-claude*
