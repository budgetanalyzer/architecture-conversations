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
- Where CLAUDE.md files live and how they load
- Request flows through multi-gateway architectures
- Navigation patterns for architects exploring the ecosystem

**Prefer**: ASCII art diagrams (version control friendly, diff-able)
**When needed**: SVG for complex visualizations

## The Reference Implementation

This repository discusses the [Budget Analyzer](https://github.com/budgetanalyzer) architecture:

**Workspace structure** (all repos cloned side-by-side):
```
/workspace/
├── orchestration/              # System coordination (this provides context)
├── service-common/             # Shared Spring Boot patterns
├── transaction-service/        # Microservice example
├── currency-service/           # Microservice example
├── permission-service/         # Microservice example
├── token-validation-service/   # JWT validation
├── session-gateway/            # BFF for browser security
├── budget-analyzer-web/        # React frontend
└── architecture-conversations/ # This repo
```

**Each service has**:
- CLAUDE.md file (AI context)
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

### When Working Here

- Read existing conversations before adding new ones
- **Never modify existing conversations** - they are historical records of actual discussions; add new conversations instead
- Maintain architect-level depth (avoid beginner explanations)
- Ground insights in production evidence (Budget Analyzer repos)
- Extract patterns when concepts are reusable
- Create visuals when relationships need clarification

### Context Loading

When discussing a specific service:
1. Read the conversation here first (why this pattern exists)
2. Then navigate to the service repo to see implementation
3. Reference both conversation and code in responses

## Key Insights Captured

**Microservices ↔ Context Windows**: Each microservice aligns with an AI context window boundary. Repository-per-service means AI loads only relevant context.

**CLAUDE.md as Architecture**: These files aren't documentation afterthoughts - they define service boundaries, discovery patterns, and AI attention zones.

**Pattern-Based Documentation**: Teach AI to discover current state, don't duplicate. Thin CLAUDE.md files with references to detailed docs.

**Autonomous AI Execution**: Clear success criteria + containerized sandbox enables AI agents to work autonomously. This is the future of development workflow.

**Open Source as AI Playground**: Public CLAUDE.md files across the internet enable AI discoverability. The ecosystem becomes AI-navigable.

## Related Documentation

- Budget Analyzer orchestration: `/workspace/orchestration/CLAUDE.md`
- Autonomous AI pattern: `/workspace/orchestration/docs/architecture/autonomous-ai-execution.md`
- Pattern-based docs: `/workspace/orchestration/docs/decisions/003-pattern-based-claude-files.md`

## For Human Readers

This CLAUDE.md is written for AI agents. For human-readable overview, see [README.md](README.md).

For contributing guidelines, see [docs/contributing.md](docs/contributing.md).

## Conversation Capture

When the user asks to save this conversation, write it to `/workspace/architecture-conversations/conversations/` following the format in INDEX.md.
