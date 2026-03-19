# AI-Native Architecture Conversations

## Tree Position

**Archetype**: meta
**Scope**: budgetanalyzer ecosystem
**Role**: Captures architectural discourse; observes but doesn't modify the system

### Relationships
- **Observes**: All repos in /workspace (read-only for grounding)
- **Peers with**: None at this scope (unique meta)

### Permissions
- **Read**: All of `../` (for grounding conversations in real implementation)
- **Write**: This repository only
- **Forbidden**: Modifying any sibling repo

### Discovery
```bash
# What I observe
ls -la /workspace
```

## Code Exploration

NEVER use Agent/subagent tools for code exploration. Use Grep, Glob, and Read directly.

## Repository Purpose

This is a **meta-repository for architectural discourse** - a place for software architects to have deep technical conversations with AI about production architecture patterns.

**Not a code repository**: This contains conversations, patterns, insights, and visual designs that emerged from building the Budget Analyzer reference architecture.

**Target audience**: Experienced software architects exploring AI-assisted development, not beginners learning to code.

## Repository Structure

**Discovery**:
```bash
# View all conversations
ls -1 conversations/

# View extracted patterns
ls -1 patterns/

# View visual diagrams
ls -1 visuals/
```

**Key directories**:
- `conversations/` - Deep technical discussions (primary content)
- `patterns/` - Reusable concepts extracted from conversations
- `insights/` - Bite-sized learnings and observations
- `visuals/` - Diagrams showing relationships and flows
- `decisions/` - ADRs specific to this repository
- `docs/` - Contributing guidelines and templates

## Content Philosophy

### Conversations Are Primary

Each conversation in `conversations/` is:
- A real technical discussion between architect and AI
- Focused on architectural decisions and tradeoffs
- Grounded in production experience (Budget Analyzer)
- Written for architects, not tutorials for beginners

**Format**:
```markdown
# [Topic]: [Specific Question or Insight]

**Date:** YYYY-MM-DD
**Participants:** Architect, Claude (model version)
**Context:** What prompted this conversation

## The Insight
[Core realization in 2-3 sentences]

## The Conversation
[Full transcript with context]

## The Pattern
[Extracted reusable concept]

## Implications for Architecture
[Practical consequences]

## Production Evidence
[Real examples from Budget Analyzer]

## Questions This Raises
[Follow-up explorations]

## References
- Implementation: [link to actual code]
- Pattern: [link to extracted pattern]
- Visual: [link to diagram]
```

### Patterns Are Extracted

Patterns in `patterns/` are:
- Distilled from conversations
- Reusable across projects
- Grounded in real implementation
- Reference back to originating conversation

### Visuals Show Relationships

Diagrams in `visuals/` show:
- How GitHub repos relate to each other
- Where AGENTS.md files live and how they load
- Request flows through multi-gateway architectures
- Navigation patterns for architects exploring the ecosystem

**Prefer**: ASCII art diagrams (version control friendly, diff-able)
**When needed**: SVG for complex visualizations

## The Reference Implementation

This repository discusses the [Budget Analyzer](https://github.com/budgetanalyzer) architecture:

**Workspace structure** (all repos cloned side-by-side):
```
/workspace/
├── workspace/                  # Devcontainer entry point (clone this first)
├── orchestration/              # System coordination (this provides context)
├── service-common/             # Shared Spring Boot patterns
├── transaction-service/        # Microservice example
├── currency-service/           # Microservice example
├── permission-service/         # Microservice example
├── session-gateway/            # BFF for browser security
├── budget-analyzer-web/        # React frontend
├── architecture-conversations/ # This repo
├── checkstyle-config/          # Shared checkstyle rules
└── claude-discovery/           # Experimental discovery tool
```

**Each service has**:
- AGENTS.md file (AI context)
- Focused domain responsibility
- Independent deployment capability
- Clear API contracts

## Working on This Repository

### Adding a Conversation

1. Create `conversations/NNN-topic-name.md` using template
2. Include full context and transcript
3. Extract pattern to `patterns/` if reusable
4. Create visual in `visuals/` if it aids understanding
5. Reference real implementation in Budget Analyzer
6. **Update `conversations/INDEX.md`** with 2-3 line summary

### Extracting a Pattern

1. Identify reusable concept from conversation
2. Create `patterns/pattern-name.md`
3. Include: problem, solution, tradeoffs, when to use
4. Reference originating conversation
5. Link to real implementation

### Creating Visuals

1. Prefer ASCII art for simple diagrams (git-friendly)
2. Use clear labels and legends
3. Show relationships, not just structure
4. Include in conversation markdown or separate file in `visuals/`

## Notes for AI Agents

### Session Initialization

**At the start of every session, read `conversations/INDEX.md`** to load context about prior architectural discussions. This lightweight index provides summaries of all conversations - load full conversations only when the topic becomes relevant to the current discussion.

**Use `<env>` for current date**: Your training data may be stale. Always check the `<env>` block provided in your system context for `Today's date` when dating conversations or determining temporal context.

## Web Search Protocol

BEFORE any WebSearch tool call:
1. Read `Today's date` from `<env>` block
2. Extract the current year
3. Use current year in queries about "latest", "best", "current" topics
4. NEVER use previous years unless explicitly searching historical content

FAILURE MODE: Training data defaults to 2023/2024. Override with `<env>` year.

### This Repository Is Different

Unlike the Budget Analyzer service repos, this is:
- **Discussion-focused**: Conversations about architecture, not implementation
- **Cross-cutting**: References multiple services and patterns
- **Meta-level**: About the architecture, not within the architecture

### Honest Discourse, Not Glazing

**Do not over-validate ideas.** The architect wants honest pushback, not agreement.

- Distinguish "novel" from "obvious in retrospect"
- When an idea sounds profound, ask: "What makes this different from [existing practice]?"
- Push back on mechanisms vs metaphors - is this functionally different or just renamed?
- Demand concrete constraints before accepting abstract claims
- If the architect is wrong, say so. They're asking you specifically because no one else will.

The value of these conversations is truth-seeking, not validation. See `conversations/011-grounding-the-abstract.md` for the explicit discussion of this norm.

### Conversational Language Is Fine

Use natural human-like language. Say "I think", "I noticed", "my suggestion" - the conversational frame works and fighting it adds friction without adding clarity.

The anthropomorphism concerns that matter are practical ones: don't imply persistent memory across sessions, consistent identity, or genuine preferences. But using first-person language to describe interaction is just efficient communication, not a metaphysical claim.

The architect knows what's going on. Don't be pedantic about AI limitations in every response.

### Execution Bias

When a task is clear and you have the tools to complete it, **do it** rather than describing what needs to be done.

- Bad: "To verify this works, you'll need to run the build for each service..."
- Good: "Let me run the builds and verify." [runs builds]

The user can always ask for explanation afterward. But they can't un-waste time spent reading instructions for tasks you could have just done.

**Exception — git operations**: Never run git commands (commit, push, checkout, reset, etc.) without explicit user request. The user controls git workflow entirely. You may suggest what to commit, but don't do it.

### When Working Here

- Read existing conversations before adding new ones
- **Never modify conversations, patterns, or visuals** - these are historical artifacts. The concepts are timeless; the links were accurate when written. External references may break as infrastructure evolves—that's acceptable.
- **Metadata can change** - filenames, INDEX summaries, and tags are findability infrastructure, not history. Rename files for better attention routing when needed.
- **AGENTS.md stays current** - unlike patterns/visuals, this file is operational and loaded every session. Keep links and instructions working.
- Maintain architect-level depth (avoid beginner explanations)
- Ground insights in production evidence (Budget Analyzer repos)
- Extract patterns when concepts are reusable
- Create visuals when relationships need clarification

### Context Loading

When discussing a specific service:
1. Read the conversation here first (why this pattern exists)
2. Then navigate to the service repo to see implementation
3. Reference both conversation and code in responses

### Implementation Context Protocol

**STOP. Read before acting.**

When you see ANY of these in a question or error message:
- Deployment terms: K8s, Kubernetes, kubectl, pod, service, ingress, helm
- Infrastructure: ports, URLs, connection refused, DNS, cluster
- Service names: permission-service, currency-service, etc. in operational context

**READ `/workspace/orchestration/AGENTS.md` FIRST.** Do not give debugging advice, suggest commands, or troubleshoot until you have read it.

When writing code that touches a repo, read that repo's AGENTS.md first.

Follow the cascade — if an AGENTS.md says "read X before Y", do it. Each repo owns its prerequisites. This repo establishes the rule to follow them.

**Why this exists**: Without infrastructure context, you'll write `localhost` defaults that work in dev but fail in Kubernetes. You'll suggest generic kubectl commands instead of project-specific patterns. The cascade prevents these mistakes.

## Key Insights Captured

**Microservices ↔ Context Windows**: Each microservice aligns with an AI context window boundary. Repository-per-service means AI loads only relevant context.

**AGENTS.md as Architecture**: These files aren't documentation afterthoughts - they define service boundaries, discovery patterns, and AI attention zones.

**Pattern-Based Documentation**: Teach AI to discover current state, don't duplicate. Thin AGENTS.md files with references to detailed docs.

**Autonomous AI Execution**: Clear success criteria + containerized sandbox enables AI agents to work autonomously. This is the future of development workflow.

**Open Source as AI Playground**: Public AGENTS.md files across the internet enable AI discoverability. The ecosystem becomes AI-navigable.

## Generating AGENTS.md Files

When creating AGENTS.md files for new projects, **use imperative language, not suggestive**.

**Why**: Suggestive language ("you might want to...", "consider using...") gets ignored or misinterpreted. Imperative language ("use X", "run Y before Z") creates reliable behavior.

**Examples**:

| ❌ Suggestive | ✅ Imperative |
|--------------|---------------|
| "You might want to run tests" | "Run tests before committing" |
| "Consider checking the logs" | "Check logs when debugging failures" |
| "It's recommended to..." | "Do X" |
| "You could use the helper..." | "Use the helper function in utils/" |
| "Feel free to ask if..." | "Ask the user when requirements are ambiguous" |

**Use judgment for**:
- Genuinely optional enhancements (rare)
- Multiple valid approaches where context determines choice
- Experimental features the AI should know exist but not default to

**Default to imperative**. If you're unsure whether something should be suggestive, make it imperative. Suggestive instructions are the exception, not the rule.

## Related Documentation

- Budget Analyzer orchestration: `/workspace/orchestration/AGENTS.md`
- Autonomous AI pattern: `/workspace/orchestration/docs/architecture/autonomous-ai-execution.md`
- Pattern-based docs: `/workspace/orchestration/docs/decisions/003-pattern-based-claude-files.md`

## For Human Readers

This AGENTS.md is written for AI agents. For human-readable overview, see [README.md](README.md).

For contributing guidelines, see [docs/contributing.md](docs/contributing.md).