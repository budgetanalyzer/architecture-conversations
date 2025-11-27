# Architecture Conversations - Gateway

This is an **entry point** for AI-native architecture patterns. Load this file into Claude Code and choose your path.

---

## Step 0: Environment Check

**Before proceeding, determine your environment.** AI execution is only safe in a sandboxed container.

### Quick Detection

Run this script to check your environment:

```bash
# Environment detection
if [ -n "$REMOTE_CONTAINERS" ] || ([ -f "/.dockerenv" ] && [ -d "/workspace" ]); then
  echo "DEVCONTAINER: You're in a sandboxed environment. Proceed to Path A or B."
elif command -v docker &> /dev/null && docker info &> /dev/null 2>&1; then
  echo "DOCKER_ONLY: Docker available but not in sandbox. See Step 0a below."
else
  echo "NO_DOCKER: No sandbox available. See Step 0b below."
fi
```

### Manual Verification

If the script doesn't work:
- **Devcontainer**: VS Code shows "Dev Container" in bottom-left corner, or you're working in `/workspace`
- **Docker available**: `docker --version` and `docker ps` both succeed
- **No Docker**: Neither command works

### Route Based on Result

| Result | Action |
|--------|--------|
| `DEVCONTAINER` | You're safe. Proceed to **Choose Your Path** below. |
| `DOCKER_ONLY` | Go to **Step 0a** to create a sandbox first. |
| `NO_DOCKER` | Go to **Step 0b** for read-only mode. |

---

## Step 0a: Create a Sandbox (Docker without Devcontainer)

You have Docker but aren't in a sandboxed environment. Before Claude can safely execute code, create an isolated sandbox.

### What You'll Create

```
~/claude-sandbox/
├── workspace/                    # Your projects go here
│   └── your-project/             # (you'll add this)
├── sandbox/                      # Sandbox configuration
│   ├── Dockerfile
│   ├── docker-compose.yml
│   ├── entrypoint.sh
│   └── README.md
└── .env                          # Your user ID (auto-generated)
```

### Step-by-Step Instructions

**1. Create the directory structure:**

```bash
mkdir -p ~/claude-sandbox/workspace
mkdir -p ~/claude-sandbox/sandbox
```

**2. Create the .env file (captures your user ID for permissions):**

```bash
cd ~/claude-sandbox/sandbox
echo "USER_UID=$(id -u)" > .env
echo "USER_GID=$(id -g)" >> .env
```

**3. Create the Dockerfile:**

Create `~/claude-sandbox/sandbox/Dockerfile` with this content:

```dockerfile
# Minimal Claude Code Sandbox
FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    curl wget git sudo ca-certificates gnupg build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://deb.nodesource.com/setup_lts.x | bash - \
    && apt-get install -y nodejs \
    && rm -rf /var/lib/apt/lists/*

ARG USERNAME=vscode
ARG USER_UID
ARG USER_GID

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m -s /bin/bash $USERNAME \
    && echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

RUN npm install -g @anthropic-ai/claude-code

RUN mkdir -p /workspace && chown -R $USER_UID:$USER_GID /workspace
RUN mkdir -p /home/$USERNAME/.anthropic && chown -R $USER_UID:$USER_GID /home/$USERNAME/.anthropic

COPY entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/entrypoint.sh

USER $USERNAME
WORKDIR /workspace

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
CMD ["/bin/bash"]
```

**4. Create the docker-compose.yml:**

Create `~/claude-sandbox/sandbox/docker-compose.yml` with this content:

```yaml
services:
  claude-sandbox:
    build:
      context: .
      dockerfile: Dockerfile
      args:
        USERNAME: vscode
        USER_UID: ${USER_UID:-1000}
        USER_GID: ${USER_GID:-1000}

    volumes:
      # Your projects (read/write)
      - ../workspace:/workspace:cached
      # This config (read-only - Claude cannot modify its own setup)
      - .:/workspace/sandbox:ro
      # Persistent credentials
      - claude-anthropic:/home/vscode/.anthropic

    network_mode: host
    command: sleep infinity
    user: vscode

volumes:
  claude-anthropic:
```

**5. Create the entrypoint.sh:**

Create `~/claude-sandbox/sandbox/entrypoint.sh` with this content:

```bash
#!/bin/bash
set -e

sudo chown -R vscode:vscode /workspace 2>/dev/null || true

mkdir -p /home/vscode/.anthropic
chmod 700 /home/vscode/.anthropic

echo ""
echo "Claude Code Sandbox"
echo "==================="
echo ""
echo "Workspace: /workspace (your projects)"
echo "Sandbox config: /workspace/sandbox (read-only)"
echo ""
echo "Run 'claude' to start."
echo ""

exec "$@"
```

**6. Build and start the sandbox:**

```bash
cd ~/claude-sandbox/sandbox
docker compose up -d --build
```

**7. Enter the sandbox:**

```bash
docker compose exec claude-sandbox bash
```

**8. Verify security:**

Inside the container, confirm the sandbox directory is read-only:
```bash
touch /workspace/sandbox/test  # Should fail with "Read-only file system"
```

### Using VS Code

You can also attach VS Code to the running container:
1. Install the "Dev Containers" extension
2. Click the green button in VS Code's bottom-left corner
3. Select "Attach to Running Container"
4. Choose `sandbox-claude-sandbox-1` (or similar)

### Add Your Project

Clone or copy your project into `~/claude-sandbox/workspace/`:
```bash
cd ~/claude-sandbox/workspace
git clone https://github.com/you/your-project.git
```

It will appear at `/workspace/your-project` inside the container.

### Cleanup

When done:
```bash
cd ~/claude-sandbox/sandbox
docker compose down
cd ~
rm -rf ~/claude-sandbox
```

**Now proceed to Choose Your Path below.**

---

## Step 0b: Read-Only Mode (No Docker)

Without Docker, there's no way to create a truly isolated sandbox. Claude will operate in **read-only mode**.

### What This Means

Claude can:
- **Read** all conversations and patterns in this repository
- **Discuss** architecture concepts with you
- **Explain** code and design patterns
- **Plan** implementations (without writing files)

Claude will NOT:
- Create or modify any files
- Execute any commands
- Generate code (only explain and discuss)

### Important: Trust Model

Without a container sandbox, if Claude were to write files, it would operate directly on your filesystem. The read-only constraint here is enforced by **AI instructions**, not system-level isolation.

**To verify Claude is respecting read-only mode:**
- Review Claude's responses - it should decline file creation requests
- Check for new files: `find . -mmin -5 -type f` (files modified in last 5 min)
- Consider this mode for reading and discussion only

**The only truly safe option is a container sandbox.**

### Installing Docker

To enable full functionality, install Docker:

| Platform | Installation |
|----------|-------------|
| macOS | [Docker Desktop for Mac](https://docs.docker.com/desktop/install/mac-install/) |
| Windows | [Docker Desktop for Windows](https://docs.docker.com/desktop/install/windows-install/) |
| Linux | [Docker Engine](https://docs.docker.com/engine/install/) |

After installing, return to **Step 0** and run the detection script again.

### Proceeding in Read-Only Mode

If you choose to continue without Docker, you can still:
- Read the curated conversations below
- Explore the patterns and archetypes
- Ask Claude questions about AI-native architecture

**Path A (Run Reference Implementation)** requires Docker.
**Path B (Generate Your Own Project)** requires Docker for safe execution.

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
