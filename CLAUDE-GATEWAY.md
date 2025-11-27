# Architecture Conversations - Gateway

This is an **entry point** for AI-native architecture patterns. Load this file into Claude Code and choose your path.

---

## Safety Constraints

This context enables **READ-ONLY exploration by default**.

- No destructive actions without explicit user approval
- Writes only to user-specified locations
- User must confirm before Claude creates files
- No modifications to external systems

---

## Choose Your Path

### Path A: Run the Reference Implementation

**What you get**: A complete microservices architecture (Budget Analyzer) demonstrating AI-native development patterns.

**Requirements**: Docker, VS Code with Dev Containers, ~30 minutes for full setup

**Start here**:
1. Visit [github.com/budgetanalyzer](https://github.com/budgetanalyzer)
2. Clone the orchestration repo
3. Open in VS Code Dev Container
4. Follow orchestration/CLAUDE.md setup instructions

**Best for**: Seeing patterns in production context, having a working reference to explore.

---

### Path B: Generate Your Own Project

**What you get**: Claude generates a complete working project in your domain using patterns from this repository.

**Requirements**: Just this file loaded into Claude Code. No cloning needed.

**How it works**: Tell Claude what you want to build. Claude will:
- Generate CLAUDE.md using appropriate archetypes
- Create build system (Gradle/Maven/npm based on your stack)
- Write source code with proper package structure
- Add tests following documented patterns
- Include container configuration if applicable

**Best for**: Starting fresh with your own domain, learning by doing.

---

## Curated Conversations (Start Here)

These five conversations capture the core insights. Read them to understand the patterns before generating:

| # | Topic | Core Insight |
|---|-------|--------------|
| [003](conversations/003-externalized-cognition.md) | Externalized Cognition | CLAUDE.md files capture expert mental models in AI-consumable form |
| [019](conversations/019-archetypes-and-scopes.md) | Archetypes and Scopes | Six stable patterns recur at every scope: meta, coordinator, platform, service, interface, experimental |
| [021](conversations/021-self-programming-via-prose.md) | Self-Programming via Prose | LLMs can be "programmed" by forcing externalization of intermediate steps |
| [023](conversations/023-asking-not-telling.md) | Asking, Not Telling | You don't tell AI what to build - you ask, then correct course |
| [029](conversations/029-natural-language-programming.md) | Natural Language Programming | CLAUDE.md files are programs written in natural language |

Full index: [conversations/INDEX.md](conversations/INDEX.md) (29 conversations)

---

## Available Patterns

| Pattern | Purpose |
|---------|---------|
| [knowledge-externalization-protocol.md](patterns/knowledge-externalization-protocol.md) | How to capture expert thinking in CLAUDE.md form |
| [context-window-driven-design.md](patterns/context-window-driven-design.md) | How to structure repositories for AI cognition |

---

## The Six Archetypes

Every CLAUDE.md fits one of these patterns. Choose based on your service's role:

| Archetype | Role | When to Use |
|-----------|------|-------------|
| **meta** | Observes, captures discourse | Documentation repos, conversation capture, meta-analysis |
| **coordinator** | Orchestrates, enables | DevOps, deployment configs, multi-service coordination |
| **platform** | Provides patterns others consume | Shared libraries, common utilities, base frameworks |
| **service** | Implements domain logic | Backend services, APIs, business logic |
| **interface** | Bridges users to system | Frontend apps, CLIs, user-facing tools |
| **experimental** | Tests hypotheses | Prototypes, experiments, methodology testing |

**Fractal pattern**: These layers are optional based on your needs:
- Single service? Just use **service** archetype
- Multi-service system? Add a **coordinator**
- Capturing learnings? Add **meta**
- Testing approaches? Add **experimental**

---

## For AI Agents: Active Generation Protocol

When a user asks to generate a project via Path B, follow this protocol:

### Step 1: Gather Context
Ask the user:
- What domain? (e.g., "equipment rental", "task management", "IoT sensor data")
- What stack? (infer from context if not specified - Spring Boot/Java is default for services)
- Single service or multi-service architecture?
- Any specific requirements? (auth, persistence, external integrations)

### Step 2: Load Patterns
Read these files to inform generation:
- `patterns/knowledge-externalization-protocol.md`
- `patterns/context-window-driven-design.md`

### Step 3: Generate Structure

**For a single service**, create:
```
service-name/
├── CLAUDE.md                    # Service context (archetype: service)
├── build.gradle.kts             # or pom.xml based on preference
├── settings.gradle.kts
├── src/main/java/
│   └── com/domain/service/
│       ├── ServiceApplication.java
│       ├── controller/
│       ├── service/
│       ├── repository/
│       └── model/
├── src/test/java/
│   └── com/domain/service/
└── docker-compose.yml           # If persistence needed
```

**For multi-service architecture**, add:
```
orchestration/
├── CLAUDE.md                    # Coordinator context (archetype: coordinator)
├── docker-compose.yml
└── docs/
```

### Step 4: Generate CLAUDE.md

Use this template, adapting to the service:

```markdown
# [Service Name]

## Tree Position

**Archetype**: [service|coordinator|platform|interface]
**Scope**: [project name]
**Role**: [One sentence describing responsibility]

### Relationships
- **Depends on**: [list dependencies]
- **Consumed by**: [list consumers]
- **Peers with**: [list peer services]

### Permissions
- **Read**: [what this context can read]
- **Write**: This repository only
- **Forbidden**: [explicit blocks]

### Discovery
```bash
# [Useful discovery commands]
```

## Purpose

[2-3 sentences on what this service does]

## Technology Stack

- Language: [Java/Kotlin/TypeScript/etc.]
- Framework: [Spring Boot/Express/etc.]
- Build: [Gradle/Maven/npm]
- Persistence: [PostgreSQL/MongoDB/etc.]

## Running Locally

```bash
# [Commands to run locally]
```

## API Reference

[Key endpoints or interfaces]

## Testing

```bash
# [Commands to run tests]
```

## Notes for AI Agents

[Service-specific guidance for Claude working in this codebase]
```

### Step 5: Generate Implementation

Write complete, working code:
- Controller with proper REST mappings
- Service layer with business logic
- Repository layer with data access
- Models/entities with appropriate annotations
- Tests using TestContainers pattern if persistence involved
- Build configuration with all dependencies

### Step 6: Iterate

After initial generation:
- Ask if the user wants adjustments
- Offer to add features
- Suggest next steps (deployment, additional services, etc.)

---

## The Fractal Insight

The architecture-conversations repo demonstrates this:

```
┌─────────────────────────────────────────┐
│  Meta (OPTIONAL)                        │
│  architecture-conversations             │
│  → Read conversations, extract wisdom   │
├─────────────────────────────────────────┤
│  Coordination (REQUIRED for full run)   │
│  orchestration                          │
│  → Only if you want the full system     │
├─────────────────────────────────────────┤
│  Implementation (OPTIONAL components)   │
│  service-common, *-service, web         │
│  → Clone what you need                  │
├─────────────────────────────────────────┤
│  Experiments (OPTIONAL)                 │
│  *-claude repos                         │
│  → For methodology testing              │
└─────────────────────────────────────────┘
```

**Key insight**: The middle is required only if you want to run everything together. You can:
- Just read conversations and generate your own thing (Path B)
- Clone individual services as templates
- Use patterns without any of the Budget Analyzer code

---

## Related Resources

- **All conversations**: [conversations/INDEX.md](conversations/INDEX.md)
- **Extracted patterns**: [patterns/](patterns/)
- **Visual diagrams**: [visuals/](visuals/)
- **Reference implementation**: [github.com/budgetanalyzer](https://github.com/budgetanalyzer)

---

## For Human Readers

This file is written for AI agents but readable by humans. The key idea:

**You don't need to understand everything here to use it.** Load this into Claude Code and say what you want to build. The generation protocol will guide the process.

If you want to understand the philosophy, start with the curated conversations above.
