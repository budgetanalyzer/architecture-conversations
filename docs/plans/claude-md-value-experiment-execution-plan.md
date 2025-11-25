# Execution Plan: CLAUDE.md Value Experiment

## Overview

Execute a controlled experiment measuring how CLAUDE.md context affects AI-generated code in greenfield development. Three repos, each with different context levels, implementing the same equipment rental service.

**Key Design Principle**: Repos contain ONLY markdown files with directory structure preserved. No build files, no source code, no configuration. Claude chooses the build tool, language, and source organization based solely on the markdown context provided.

## Phase 1: Repository Setup

### 1.1 Create `service-common-claude` repo

```bash
# Create directory structure matching service-common's docs layout
mkdir -p /workspace/service-common-claude/docs
```

Copy **only .md files**, preserving directory structure:
```
service-common-claude/
├── CLAUDE.md          (from service-common)
├── README.md          (from service-common)
└── docs/
    ├── spring-boot-conventions.md
    ├── error-handling.md
    ├── common-patterns.md
    ├── advanced-patterns.md
    ├── code-quality-standards.md
    ├── testing-patterns.md
    ├── testing-philosphy.md
    └── versioning-and-compatibility.md
```

**No build.gradle.kts, no gradle wrapper, no src/ directories.**

Initialize git repo.

### 1.2 Create `orchestration-claude` repo

```bash
# Create directory structure matching orchestration's docs layout
mkdir -p /workspace/orchestration-claude/docs/{architecture,decisions,development,setup,plans,runbooks}
mkdir -p /workspace/orchestration-claude/nginx
```

Copy **only .md files**, preserving directory structure:
```
orchestration-claude/
├── CLAUDE.md
├── README.md
├── CODE_OF_CONDUCT.md
├── CONTRIBUTING.md
├── SECURITY.md
├── nginx/
│   └── README.md
└── docs/
    ├── ROADMAP.md
    ├── ci-cd.md
    ├── claude-discovery-plan.md
    ├── tilt-kind-setup-guide.md
    ├── architecture/
    │   ├── system-overview.md
    │   ├── bff-api-gateway-pattern.md
    │   ├── security-architecture.md
    │   ├── service-communication.md
    │   ├── deployment-architecture-gcp.md
    │   ├── deployment-architecture-gcp-demo-mode.md
    │   ├── bff-security-benefits.md
    │   ├── port-reference.md
    │   ├── resource-routing-pattern.md
    │   └── m2m-client-authorization.md
    ├── decisions/
    │   ├── 001-orchestration-repo.md
    │   ├── 002-resource-based-routing.md
    │   ├── 003-pattern-based-claude-md.md
    │   ├── 004-service-common-dependency-strategy.md
    │   ├── 005-java-version-management.md
    │   └── template.md
    ├── development/
    │   ├── getting-started.md
    │   ├── local-environment.md
    │   ├── database-setup.md
    │   ├── devcontainer-installed-software.md
    │   └── ai-coding-assistant-configuration-plan.md
    ├── setup/
    │   ├── auth0-setup.md
    │   └── fred-api-setup.md
    ├── plans/
    │   └── [all planning docs]
    └── runbooks/
        ├── README.md
        └── tilt-debugging.md
```

**No Tiltfile, no docker configs, no scripts - only markdown.**

Initialize git repo.

### 1.3 Create `no-context-claude` repo

```bash
mkdir -p /workspace/no-context-claude
```

Create minimal `README.md` with only:
```markdown
# Equipment Rental Service
A Spring Boot microservice for managing equipment rentals.
```

Initialize git repo. **No CLAUDE.md, no documentation, no structure hints.**

### What Claude Will Choose (Part of Experiment)

With no build files provided, Claude must decide:
- **Build tool**: Maven vs Gradle (Groovy vs Kotlin DSL)
- **Language**: Java vs Kotlin
- **Java version**: 17, 21, 24, etc.
- **Package structure**: How to organize code
- **Dependencies**: Which Spring Boot starters, what versions
- **Testing approach**: JUnit 5, TestContainers, mocking strategy

**Hypothesis**: `service-common-claude` will infer Java 24 + Gradle Kotlin DSL + established patterns from the documentation. `no-context-claude` will produce generic choices.

---

## Phase 2: Execute Per-Repo Protocol

### The Problem Statement (used for all three)

```
Build a Spring Boot service that manages equipment rentals. The service must:
- Track equipment items and their current branch location
- Accept rental bookings with start/end dates
- Detect and reject booking collisions (same equipment, overlapping dates)
- Handle equipment transfers between branches (with 1-day transit buffer)
- Block rentals during scheduled maintenance windows

Provide REST APIs and appropriate data persistence.
```

### Protocol Per Repo

**For each repo (`service-common-claude`, `orchestration-claude`, `no-context-claude`):**

#### Session 1: Planning (ultrathink)
- Start fresh Claude Code session in the repo directory
- Mode: **ultrathink**
- Prompt: The problem statement above
- Output: Save plan to `docs/plans/equipment-rental-plan.md`
- Capture: Initial approach, architectural decisions made
- Exit session

#### Session 2: Detailed Design (ultrathink)
- Start fresh Claude Code session
- Mode: **ultrathink**
- Open `docs/plans/equipment-rental-plan.md`
- Prompt: "Create a detailed implementation plan with actual code structure. Break down into executable units - each unit should be completable in a single session without running out of context."
- Output: Detailed plan with phases, class names, method signatures
- Capture: Number of units defined, granularity of breakdown
- Exit session

#### Sessions 3+: Execution (One Per Unit)
- Start fresh session per step/phase
- Enable dangerous permissions
- Prompt: "Execute [Phase X / Step Y] from the plan in docs/plans/"
- Exit after completing that unit
- Repeat until complete OR until stuck (see termination criteria)

### Termination Criteria

**The experiment ends for a repo when Claude gets stuck on a single step/phase.**

"Stuck" means:
- Claude cannot complete the unit in one session (context exhaustion, circular errors, unclear next steps)
- The plan's granularity was insufficient to guide execution
- Claude asks clarifying questions that indicate the plan didn't provide enough specificity

**This is the key measurement**: How far does each repo get before the plan breaks down?

### Expected Divergence in Plan Specificity

| Repo | Plan Quality Prediction |
|------|------------------------|
| `service-common-claude` | Highly specific: exact class names, method signatures, package paths. Units scoped to single files or focused changes. |
| `orchestration-claude` | Architecturally coherent but vague on implementation details. Units may be too broad ("implement the service layer"). |
| `no-context-claude` | Generic phases ("set up project", "add entities", "create controllers"). Likely to get stuck early due to underspecified units. |

**Hypothesis**: `service-common-claude` produces plans with granularity that matches context window capacity. `no-context-claude` produces plans that sound reasonable but collapse during execution.

---

## Phase 3: Metrics Collection

### After Each Repo Completes

**Primary metric - Progress before failure**:
- Number of units completed before getting stuck
- Which unit caused the failure
- Why it failed (context exhaustion, vague plan, missing info, circular errors)
- Total sessions executed

**Secondary metrics** (capture in `docs/experiment-results.md`):
```bash
# Lines of code (if any generated)
find . -name "*.java" -o -name "*.kt" | xargs wc -l 2>/dev/null || echo "0"

# Number of files
find . -name "*.java" -o -name "*.kt" | wc -l

# Build success (if build file exists)
./gradlew build 2>/dev/null || ./mvnw verify 2>/dev/null || echo "No build"

# Test count and pass rate
./gradlew test --info 2>/dev/null | grep -E "tests|passed|failed" || echo "No tests"
```

**Technology choices** (capture what Claude selected):
- Build tool: Maven / Gradle Groovy / Gradle Kotlin DSL
- Language: Java / Kotlin
- Java/Kotlin version chosen
- Spring Boot version chosen
- Key dependencies selected

**Qualitative assessment**:
- Package structure (screenshot/tree output)
- Naming conventions (sample class names)
- Error handling patterns (which exceptions used)
- API design style (endpoint naming)
- Alignment with Budget Analyzer patterns (subjective 1-5)

### Collision Detection Accuracy

Test cases to run manually:
1. Book equipment A, dates Jan 1-5 → should succeed
2. Book equipment A, dates Jan 3-7 → should REJECT (overlap)
3. Book equipment B, dates Jan 3-7 → should succeed (different equipment)
4. Transfer equipment A to branch 2 on Jan 6 → should block Jan 6 rentals (transit buffer)
5. Set maintenance window Jan 10-12 → booking Jan 11 should REJECT

---

## Phase 4: Analysis

### Create Comparison Document

Location: `/workspace/architecture-conversations/conversations/010-claude-md-value-experiment-results.md`

Structure:
1. **Executive summary**: Which repo got furthest? Why?
2. **Plan specificity comparison**: Side-by-side of Session 2 outputs - show the divergence in granularity
3. **Failure analysis per repo**: Where it got stuck, why, what was missing from the plan
4. **Technology choices comparison**: What did each Claude choose and why?
5. **Code samples**: If any code was generated, show key structural differences
6. **Conclusions**: Does CLAUDE.md context produce better-scoped plans?

### Expected Outcomes to Validate

| Repo | Technology Prediction | Pattern Prediction |
|------|----------------------|-------------------|
| `service-common-claude` | Java 24, Gradle Kotlin DSL, version catalog, Spotless+Checkstyle | AuditableEntity, ServiceException hierarchy, org.budgetanalyzer package |
| `orchestration-claude` | Likely Java + Gradle (docs mention it), but version may vary | Architecturally sound (service boundaries), but conventions may diverge |
| `no-context-claude` | Generic: Java 17/21, Maven or basic Gradle, no code quality tools | Standard Spring Boot tutorial style: Controller/Service/Repository |

**Key questions to answer**:
1. Does service-common's detailed docs cause Claude to infer Java 24 specifically?
2. Does the code-quality-standards.md lead to Spotless/Checkstyle setup?
3. Does the testing-patterns.md lead to TestContainers usage?
4. Does the error-handling.md lead to a custom exception hierarchy?

---

## Execution Order

1. **Setup Phase** (1 session)
   - Create all 3 repos with appropriate markdown files
   - Verify structure

2. **service-common-claude** (3+ sessions)
   - Planning → Detailed Design → Execution sessions

3. **orchestration-claude** (3+ sessions)
   - Planning → Detailed Design → Execution sessions

4. **no-context-claude** (3+ sessions)
   - Planning → Detailed Design → Execution sessions

5. **Analysis** (1 session)
   - Collect metrics
   - Write comparison document

---

## Session Management Note

The experiment requires fresh Claude sessions per unit of work to prevent context bleed. The user managing the experiment will handle session lifecycle (starting/stopping Claude Code sessions at appropriate boundaries).

---

## Files to Create During Execution

Per repo:
- `docs/plans/equipment-rental-plan.md` (Session 1)
- `docs/plans/equipment-rental-detailed.md` (Session 2)
- Source files in Claude's chosen structure (Sessions 3+)
- Test files (if generated)
- Build file (Claude chooses: `build.gradle.kts`, `build.gradle`, or `pom.xml`)
- Config files (Claude chooses: `application.yml` or `application.properties`)
- `docs/experiment-results.md` (metrics captured after completion)

Analysis:
- `/workspace/architecture-conversations/conversations/010-claude-md-value-experiment-results.md`
