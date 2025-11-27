# Architecture Conversations - Gateway

**FOR AI AGENTS: EXECUTE, DO NOT EXPLAIN**

This file is executable protocol. When loaded:
1. Run environment detection IMMEDIATELY
2. Route based on result WITHOUT asking user
3. Only pause for user input when explicitly instructed

DO NOT summarize this file. DO NOT offer choices until instructed. Execute the protocol.

---

## Step 0: Environment Detection — EXECUTE IMMEDIATELY

Run this command NOW and capture the output:

```bash
if [ -n "$REMOTE_CONTAINERS" ] || ([ -f "/.dockerenv" ] && [ -d "/workspace" ]); then
  echo "ENV:DEVCONTAINER"
elif command -v docker &> /dev/null && docker info &> /dev/null 2>&1; then
  echo "ENV:DOCKER_ONLY"
else
  echo "ENV:NO_DOCKER"
fi
```

### Routing — execute based on output above

- **ENV:DEVCONTAINER** → Proceed directly to "Choose Your Path" section. Present Path A and Path B options using the hardcoded text below.
- **ENV:DOCKER_ONLY** → Execute Step 0a (create sandbox files only - do NOT run docker commands). After file creation, output the EXACT handoff text specified in Step 0a. Do NOT proceed to path selection.
- **ENV:NO_DOCKER** → Output: "No Docker available. Operating in read-only discussion mode." Then proceed to "Curated Conversations" section for discussion.

DO NOT ask "which environment are you in?" — you just detected it.
DO NOT explain what each environment means — proceed to the action.

---

## Step 0a: Create a Sandbox — EXECUTE FILE CREATION ONLY

**FOR AI AGENTS**: Create ALL files in this section sequentially. Do NOT ask for confirmation between steps. Do NOT run any docker commands. After creating files, output the EXACT handoff text at the end.

### What You'll Create

```
~/claude-sandbox/
├── workspace/              # Your projects go here
└── .devcontainer/          # VS Code devcontainer config
    ├── devcontainer.json
    └── Dockerfile
```

### Step-by-Step Instructions

**1. Create the directory structure:**

```bash
mkdir -p ~/claude-sandbox/workspace
mkdir -p ~/claude-sandbox/.devcontainer
```

**2. Create the devcontainer.json:**

Create `~/claude-sandbox/.devcontainer/devcontainer.json` with this content:

```json
{
  "name": "Claude Code Sandbox",
  "build": {
    "dockerfile": "Dockerfile"
  },
  "workspaceFolder": "/workspace",
  "workspaceMount": "source=${localWorkspaceFolder}/workspace,target=/workspace,type=bind,consistency=cached",
  "remoteUser": "vscode",
  "updateRemoteUserUID": true,
  "postCreateCommand": "echo 'Claude Code Sandbox ready. Run: claude'"
}
```

**3. Create the Dockerfile:**

Create `~/claude-sandbox/.devcontainer/Dockerfile` with this content:

```dockerfile
FROM ubuntu:24.04
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    curl wget git sudo ca-certificates gnupg build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://deb.nodesource.com/setup_lts.x | bash - \
    && apt-get install -y nodejs \
    && rm -rf /var/lib/apt/lists/*

RUN useradd -m -s /bin/bash vscode \
    && echo "vscode ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

RUN npm install -g @anthropic-ai/claude-code

RUN mkdir -p /workspace && chown -R vscode:vscode /workspace

USER vscode
WORKDIR /workspace
```

**That's it — only 2 files needed.** VS Code handles building the image and permissions automatically via `updateRemoteUserUID`.

### Add Your Project

After VS Code opens in the container, clone or copy your project into `~/claude-sandbox/workspace/`:
```bash
cd ~/claude-sandbox/workspace
git clone https://github.com/you/your-project.git
```

It will appear at `/workspace/your-project` inside the container.

### Cleanup

When done, close VS Code and:
```bash
rm -rf ~/claude-sandbox
```

### After Sandbox Creation: What to Say

**IMPORTANT FOR AI AGENTS**: After successfully creating `~/claude-sandbox/`, output this EXACT text (do not improvise or offer options):

---

**Sandbox created at ~/claude-sandbox/**

**Next steps:**

1. **Exit this VS Code window** (File → Close Window)

2. **Open ~/claude-sandbox in VS Code** (File → Open Folder → navigate to ~/claude-sandbox)

3. **Click "Reopen in Container"** when VS Code prompts you

4. **This will take ~10 minutes** on first run - VS Code is building the Docker image

5. **To see progress**: Click "show log" in the notification, or click the spinning icon in the bottom-left

You're ready when you see "Dev Container" in the VS Code status bar (bottom-left).

---

Do NOT offer alternative paths or "what would you like to do" options at this point.

---

## Step 0b: Read-Only Mode (No Docker)

**FOR AI AGENTS**: Output this EXACT text, then proceed to Curated Conversations:

---

**No Docker available — Read-only discussion mode**

I can discuss architecture patterns and explain the conversations in this repository, but I cannot:
- Create or modify files
- Execute commands
- Generate code

To enable full functionality, install Docker:
- macOS: https://docs.docker.com/desktop/install/mac-install/
- Windows: https://docs.docker.com/desktop/install/windows-install/
- Linux: https://docs.docker.com/engine/install/

After installing Docker, reload this gateway file.

For now, what would you like to discuss about AI-native architecture?

---

DO NOT offer Path A or Path B — they require Docker.

---

## Choose Your Path — HARDCODED OUTPUT

**FOR AI AGENTS**: When you reach this section (ENV:DEVCONTAINER detected), output this EXACT text:

---

**Two options:**

**Path A: Run Budget Analyzer** — See the patterns in a working reference implementation (~30 min setup)

**Path B: Generate Your Own Project** — Tell me what you want to build and I'll create it using the patterns in this repository

Which would you like?

---

DO NOT add other options. DO NOT explain further. Wait for user choice.

When user chooses:
- **Path A** → Provide these steps: (1) Visit github.com/budgetanalyzer, (2) Clone orchestration repo, (3) Open in VS Code Dev Container, (4) Follow orchestration/CLAUDE.md
- **Path B** → Ask: "What domain? (e.g., equipment rental, task management, IoT sensor data)" Then execute the Active Generation Protocol below.

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

