# Git Authentication for Autonomous AI Agents

## Problem Statement

The devcontainer provides a fully autonomous sandbox for AI agents - they can read all repos, write files, run tests, build services. But they cannot `git push`. This is the last mile to true autonomous workflow.

**The question**: Should this be:
1. Documented as an intentional constraint ("you have no git auth, work within this")
2. Solved by giving the container a GitHub identity

## Decision

**Approach**: GitHub machine user account with Personal Access Token (PAT), passed via environment variable.

**Security model**: Branch protection prevents pushes to main. Bot can only push feature branches and create PRs. Human review happens at PR time, not commit time.

**Rationale**:
- No friction in the development loop (AI can push immediately)
- Security comes from branch protection, not access restriction
- Environment variable means you control when push is enabled
- Distinct bot identity provides clear audit trail

---

## The Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  GitHub                                                      │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  budgetanalyzer-bot (machine user account)          │    │
│  │  - Collaborator on all repos                        │    │
│  │  - PAT with repo scope                              │    │
│  └─────────────────────────────────────────────────────┘    │
│                          │                                   │
│                          │ GITHUB_TOKEN env var              │
│                          ▼                                   │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  Devcontainer                                        │    │
│  │                                                      │    │
│  │  Claude Agent                                        │    │
│  │       │                                              │    │
│  │       ├──► git push ──► feature branches only       │    │
│  │       │                 (main blocked by protection) │    │
│  │       │                                              │    │
│  │       └──► gh pr create ──► Human reviews ──► Merge │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                              │
│  Branch Protection (all repos):                             │
│  └─ main: Require PR, require review, no direct push        │
└─────────────────────────────────────────────────────────────┘
```

---

## Alternatives Considered

### Option A: Document as Constraint

Treat no-auth as a deliberate security feature. The friction of manual push IS the checkpoint.

**Arguments for**:
- Local mistakes are trivial to undo (`git checkout -- .`)
- Pushed mistakes are permanent records
- Human review happens at natural pause point
- Clear attribution (human is author)

**Arguments against**:
- Creates friction in a workflow designed for autonomy
- PR review provides equivalent safety
- The 11-repo push ceremony is tedious

**Verdict**: Valid for high-security environments, but undermines the autonomous vision.

### Option B: SSH Agent Forwarding

Forward host's SSH agent into container.

**Arguments for**:
- Works immediately with existing SSH keys
- No new accounts to create

**Arguments against**:
- Commits attributed to human, not bot
- Depends on host SSH setup
- User explicitly rejected this approach

### Option C: GitHub App

Create a GitHub App with fine-grained permissions.

**Arguments for**:
- Better audit trail
- Fine-grained permissions
- Automatic token refresh

**Arguments against**:
- More setup complexity (JWT signing, installation tokens)
- Overkill for single-org, 11-repo setup
- PAT is simpler for this scale

---

## Implementation Steps

### 1. Create GitHub Machine User Account

**One-time setup on GitHub:**

1. Create new GitHub account: `budgetanalyzer-bot` (or similar)
   - Use a dedicated email (e.g., `bot@yourdomain.com` or alias)

2. Add as collaborator to all repos
   - Permission level: **Write** (can push, can't admin)

3. Generate Personal Access Token (classic):
   - Scopes: `repo` (full repo access)
   - Expiration: 90 days (set reminder to rotate)

### 2. Configure Container to Use Token

**docker-compose.yml**:
```yaml
services:
  claude-dev:
    environment:
      - GITHUB_TOKEN=${GITHUB_TOKEN:-}
```

**entrypoint.sh**:
```bash
if [ -n "$GITHUB_TOKEN" ]; then
    # Configure git credentials
    git config --global credential.helper store
    echo "https://budgetanalyzer-bot:${GITHUB_TOKEN}@github.com" > ~/.git-credentials
    chmod 600 ~/.git-credentials

    # Set bot identity
    git config --global user.name "budgetanalyzer-bot"
    git config --global user.email "budgetanalyzer-bot@users.noreply.github.com"

    # Authenticate gh CLI
    echo "$GITHUB_TOKEN" | gh auth login --with-token

    echo "Git push: ENABLED"
else
    echo "Git push: DISABLED (no GITHUB_TOKEN)"
fi
```

### 3. Convert Remotes from SSH to HTTPS

Token auth requires HTTPS remotes. Add to entrypoint.sh:

```bash
for repo in /workspace/*/; do
    if [ -d "$repo/.git" ]; then
        cd "$repo"
        url=$(git remote get-url origin 2>/dev/null || true)
        if [[ "$url" == git@github.com:* ]]; then
            https_url=$(echo "$url" | sed 's|git@github.com:|https://github.com/|')
            git remote set-url origin "$https_url"
        fi
        cd - >/dev/null
    fi
done
```

### 4. Install GitHub CLI

**Dockerfile**:
```dockerfile
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
    | dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
    | tee /etc/apt/sources.list.d/github-cli.list > /dev/null && \
    apt-get update && apt-get install -y gh && \
    rm -rf /var/lib/apt/lists/*
```

### 5. Set Up Branch Protection

For each repository, protect `main`:
- Require pull request reviews before merging (1 reviewer)
- Dismiss stale approvals when new commits pushed
- Require status checks to pass (if CI exists)
- Do not allow bypassing the above settings

### 6. Document in CLAUDE.md Files

Add to orchestration/CLAUDE.md:

```markdown
### Git Push Capabilities

**Identity**: This container operates as `budgetanalyzer-bot` when GITHUB_TOKEN is set.

**Security model**: Branch protection, not access restriction.

**Allowed**:
- Push to feature branches (prefix: `claude/`)
- Create pull requests via `gh pr create`
- Commit with bot identity

**Forbidden** (enforced by GitHub):
- Push directly to `main` (branch protection)
- Merge without human review
- Force push to any branch

**Workflow**:
1. Create branch: `git checkout -b claude/feature-description`
2. Make changes, commit
3. Push: `git push -u origin claude/feature-description`
4. Create PR: `gh pr create --fill`
5. Human reviews and merges
```

---

## Usage

### With Push Access
```bash
export GITHUB_TOKEN="ghp_xxxxxxxxxxxxxxxxxxxx"
docker compose up -d
```

### Without Push Access (Read-Only)
```bash
# Just don't set GITHUB_TOKEN
docker compose up -d
```

---

## Security Considerations

| Risk | Mitigation |
|------|------------|
| Token leaked in logs | Never echo $GITHUB_TOKEN |
| Bot pushes to main | Branch protection blocks it |
| Token in git history | Token in env var, not committed |
| Stale token | Rotate every 90 days |
| Bot runs amok | Revoke PAT = instant disable |

---

## Rollback

1. **Immediate**: Revoke PAT on GitHub
2. **Cleanup**: `git push origin --delete claude/bad-branch`
3. **Close PRs**: `gh pr close <number>`

Main is always protected. Worst case is garbage feature branches.

---

## Files to Modify

| File | Change |
|------|--------|
| workspace/claude-code-sandbox/docker-compose.yml | Add GITHUB_TOKEN env var |
| workspace/claude-code-sandbox/entrypoint.sh | Git credential setup, remote conversion |
| workspace/claude-code-sandbox/Dockerfile | Install gh CLI |
| orchestration/CLAUDE.md | Document git capabilities |
| GitHub settings | Create bot account, branch protection |

---

## Related

- devcontainer-simplification.md - The workspace structure this builds on
- /workspace/orchestration/docs/architecture/autonomous-ai-execution.md - Broader autonomous AI pattern
