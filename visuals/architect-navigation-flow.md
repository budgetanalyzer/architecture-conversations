# Architect Navigation Flow

Visual representation of how architects explore the Budget Analyzer ecosystem.

```
HOW ARCHITECTS EXPLORE THIS ECOSYSTEM
══════════════════════════════════════

Entry Point: "I want to understand microservices + AI"
                        │
                        ▼
        ┌───────────────────────────────┐
        │  architecture-conversations/  │
        │  README.md                    │
        │  "Start here"                 │
        └───────────────┬───────────────┘
                        │
        ┌───────────────┴───────────────┐
        │                               │
        ▼                               ▼
┌───────────────────┐         ┌──────────────────────┐
│  conversations/   │         │  visuals/            │
│  001-microservices│         │  ecosystem-overview  │
│  -context-windows │         │  .md                 │
│  .md              │         │                      │
│  "Deep dive"      │         │  "Visual overview"   │
└─────────┬─────────┘         └──────────────────────┘
          │
          │ References pattern
          ▼
┌────────────────────┐
│  patterns/         │
│  context-window-   │
│  driven-design.md  │
│  "Reusable"        │
└─────────┬──────────┘
          │
          │ Points to implementation
          ▼
┌────────────────────────────┐
│  budgetanalyzer/           │
│  transaction-service/      │
│  "Real code"               │
│                            │
│  Read: CLAUDE.md           │
│  Read: docs/csv-import.md  │
│  Read: src/main/java/...  │
└────────────────────────────┘
```

## Navigation Pattern

### 1. Conversation (Why + What)
Start with deep technical discussion in `conversations/`:
- Understand the architectural insight
- See the reasoning and tradeoffs
- Follow the thought process
- Get production context

### 2. Pattern Extraction (Reusable Concept)
Move to `patterns/` for actionable guidance:
- Problem statement
- Solution approach
- When to use / when not to use
- Implementation considerations

### 3. Visual Design (Relationships)
Check `visuals/` for diagrams:
- How components relate
- Request flows
- Repository structure
- Context loading hierarchy

### 4. Implementation (Real Code)
Navigate to actual Budget Analyzer repos:
- See pattern in production
- Read CLAUDE.md files
- Study real implementations
- Learn from working code

## Example Navigation Journeys

### Journey 1: Understanding AI-Native Architecture

```
Start: README.md
  ↓
Read: conversations/001-microservices-context-windows.md
  ↓
  ├→ View: visuals/ecosystem-overview.md
  ├→ View: visuals/claude-md-hierarchy.md
  ↓
Extract: patterns/context-window-driven-design.md
  ↓
Implement: Look at transaction-service/CLAUDE.md
  ↓
Study: Compare with currency-service/CLAUDE.md
  ↓
Understand: orchestration/CLAUDE.md ties them together
```

### Journey 2: Implementing BFF + API Gateway Pattern

```
Start: README.md mentions "BFF + API Gateway"
  ↓
Read: conversations/006-bff-security-rationale.md (future)
  ↓
View: visuals/request-flow-bff-gateway.md
  ↓
Extract: patterns/bff-api-gateway-hybrid.md (future)
  ↓
Implement: Study orchestration/nginx/nginx.k8s.conf
  ↓
Study: session-gateway/ implementation
  ↓
Understand: How JWT never reaches browser
```

### Journey 3: Learning Pattern-Based Documentation

```
Start: Curious about CLAUDE.md philosophy
  ↓
Read: CLAUDE.md in this repo
  ↓
View: visuals/claude-md-hierarchy.md
  ↓
Read: orchestration/docs/decisions/003-pattern-based-claude-files.md
  ↓
Study: Compare CLAUDE.md files across repos:
  ├→ orchestration/CLAUDE.md (system patterns)
  ├→ service-common/CLAUDE.md (shared patterns)
  └→ transaction-service/CLAUDE.md (thin, references common)
  ↓
Extract: patterns/pattern-based-documentation.md (future)
```

## Content Types and Their Purpose

### Conversations (`conversations/`)
**Purpose**: Deep technical discourse
**Audience**: Architects exploring AI-assisted development
**Length**: 2000-5000 words
**Style**: Dialogue format, philosophical yet practical
**Contains**: Full context, reasoning, production evidence

### Patterns (`patterns/`)
**Purpose**: Reusable architectural concepts
**Audience**: Practitioners implementing solutions
**Length**: 500-1500 words
**Style**: Problem/Solution/Tradeoffs format
**Contains**: When to use, implementation guide, references

### Insights (`insights/`)
**Purpose**: Bite-sized observations
**Audience**: Quick learners, scanners
**Length**: 200-500 words
**Style**: Punchy, focused, memorable
**Contains**: Key takeaway, example, reference to deeper content

### Visuals (`visuals/`)
**Purpose**: Show relationships and flows
**Audience**: Visual thinkers, system designers
**Format**: ASCII art (preferred) or SVG
**Contains**: Diagrams, flows, hierarchies, mappings

### Decisions (`decisions/`)
**Purpose**: ADRs for this repository's structure
**Audience**: Contributors, maintainers
**Length**: 300-800 words
**Style**: Standard ADR format
**Contains**: Context, decision, consequences, status

## Cross-Repository Navigation

### From architecture-conversations to Budget Analyzer repos

When reading conversations/patterns in this repo, you'll see references like:

```markdown
Implementation: [transaction-service/CLAUDE.md](https://github.com/budgetanalyzer/transaction-service/blob/main/CLAUDE.md)
```

This means:
1. The concept is discussed here (architecture-conversations)
2. Real implementation lives in Budget Analyzer repos
3. You can study working code, not just theory

### From Budget Analyzer repos to architecture-conversations

When working in a Budget Analyzer service, you might see:

```markdown
For architectural rationale, see:
[architecture-conversations/conversations/001-microservices-context-windows.md](https://github.com/budgetanalyzer/architecture-conversations/blob/main/conversations/001-microservices-context-windows.md)
```

This means:
1. You're looking at implementation (service repo)
2. To understand WHY it's built this way, read the conversation
3. The pattern is documented and explained in detail

## AI Agent Navigation

### How AI discovers this ecosystem

```
1. AI loads: architecture-conversations/CLAUDE.md
   ↓
   Understands: This is meta-repo for architectural discourse

2. AI reads: README.md
   ↓
   Discovers: conversations/, patterns/, visuals/

3. When asked about a topic, AI searches:
   ├→ conversations/ for deep discussion
   ├→ patterns/ for reusable concept
   └→ visuals/ for diagrams

4. AI references Budget Analyzer repos:
   ├→ For implementation details
   ├→ For production evidence
   └→ For working examples

5. AI can navigate bidirectionally:
   ├→ From concept (here) to code (Budget Analyzer)
   └→ From code (Budget Analyzer) to rationale (here)
```

## Key Insight

This repository serves as the **"why"** layer:
- **Why** microservices align with AI context windows
- **Why** CLAUDE.md files matter architecturally
- **Why** certain patterns emerged
- **Why** specific decisions were made

Budget Analyzer repos serve as the **"what"** and **"how"** layer:
- **What** the actual implementation looks like
- **How** to build production systems with these patterns

Together, they form a complete learning resource for AI-native architecture.

## For Contributors

When adding new content:

1. **Conversations**: Full context, real dialogue, production evidence
2. **Patterns**: Extract reusable concepts, reference conversation
3. **Visuals**: Create diagrams that clarify relationships
4. **Cross-link**: Reference Budget Analyzer implementations

Make navigation easy by:
- Clear references between files
- Consistent linking patterns
- Descriptive file names
- Table of contents in long docs
