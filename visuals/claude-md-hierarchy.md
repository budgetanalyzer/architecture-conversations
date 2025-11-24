# CLAUDE.md Hierarchy and Loading Pattern

Visual representation of how AI context is loaded hierarchically across repositories.

```
AI CONTEXT LOADING PATTERN
═══════════════════════════

┌─────────────────────────────────────────────────────────────┐
│  Architect Working in transaction-service/                  │
│                                                              │
│  CONTEXT LOADED (Hierarchical):                             │
│                                                              │
│  1. /workspace/orchestration/CLAUDE.md        [Always]      │
│     ├─ System architecture patterns                         │
│     ├─ Gateway routing patterns                             │
│     ├─ BFF + API Gateway hybrid pattern                     │
│     └─ Discovery commands (grep, kubectl, etc)              │
│                                                              │
│  2. /workspace/service-common/CLAUDE.md      [Subtree]      │
│     ├─ Spring Boot conventions                              │
│     ├─ Testing patterns (TestContainers)                    │
│     ├─ Security patterns (JWT, RBAC)                        │
│     └─ References to docs/spring-boot-conventions.md        │
│                                                              │
│  3. /workspace/transaction-service/CLAUDE.md [Subtree]      │
│     ├─ "Follows service-common patterns" (reference)        │
│     ├─ Service-specific: CSV import feature                 │
│     ├─ Service-specific: Transaction domain logic           │
│     └─ References to docs/csv-import.md                     │
│                                                              │
│  JUST-IN-TIME LOADING (when Claude needs detail):           │
│                                                              │
│  4. transaction-service/docs/csv-import.md   [On demand]    │
│     └─ Full CSV parsing implementation details              │
│                                                              │
│  5. service-common/docs/testing-patterns.md  [On demand]    │
│     └─ Complete TestContainers examples                     │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

## Token Efficiency

```
PATTERN-BASED APPROACH (Used by Budget Analyzer):
═════════════════════════════════════════════════

Initial context (3 CLAUDE.md files):
  orchestration/CLAUDE.md:           ~2,000 tokens
  service-common/CLAUDE.md:          ~1,500 tokens
  transaction-service/CLAUDE.md:     ~1,500 tokens
                                     ───────────────
  Total initial context:             ~5,000 tokens

Just-in-time detail (loaded as needed):
  docs/csv-import.md:                ~3,000 tokens
  docs/testing-patterns.md:          ~2,500 tokens


TRADITIONAL APPROACH (Everything in one CLAUDE.md):
═══════════════════════════════════════════════════

Single monolithic file:
  CLAUDE.md:                        ~25,000 tokens
  - System patterns
  - All service details
  - All Spring patterns
  - All implementation details
  - Everything loaded upfront


SAVINGS WITH PATTERN-BASED APPROACH:
════════════════════════════════════

Initial context:        5,000 tokens vs 25,000 tokens
Reduction:             80% less context loaded
On-demand loading:     Only load what's needed
Result:                More focused AI attention
```

## Loading Hierarchy

### Level 1: System (Always Loaded)
**orchestration/CLAUDE.md**
- Gateway routing patterns
- Service discovery methods
- Development workflow (Tilt, Kind)
- Cross-service concerns

### Level 2: Shared Patterns (Subtree)
**service-common/CLAUDE.md**
- Spring Boot conventions
- Testing patterns
- Security patterns
- Build system (Gradle)

### Level 3: Service-Specific (Subtree)
**{service-name}/CLAUDE.md**
- Unique service concerns
- Domain-specific logic
- References to service-common
- API contracts

### Level 4: Detailed Documentation (Just-in-Time)
**{service-name}/docs/*.md**
- Implementation details
- Architecture decision records
- Feature specifications
- Only loaded when AI needs specifics

## Why This Works

### For AI Agents
- **Focused attention**: Only relevant context loaded
- **Scalable**: Works with 8 services or 80 services
- **Discoverable**: References guide to deeper docs
- **Efficient**: 80% reduction in initial context

### For Human Architects
- **Maintainable**: Thin files easier to keep current
- **Pattern-based**: Teach discovery, don't duplicate
- **Living documentation**: Survives refactoring
- **Clear boundaries**: Each file has specific purpose

## Example Workflow

**Scenario**: AI agent needs to add a new CSV import feature to transaction-service

1. **Load orchestration/CLAUDE.md** - Understand system patterns
2. **Load service-common/CLAUDE.md** - Learn Spring Boot conventions
3. **Load transaction-service/CLAUDE.md** - Understand existing CSV import
4. **Just-in-time: Load docs/csv-import.md** - Get implementation details
5. **Work on code** - AI has focused context for this specific task

**Total context loaded**: ~8,500 tokens (instead of 25,000 in traditional approach)

## Key Principles

1. **Thin root files** - CLAUDE.md files stay under 200 lines
2. **References, not duplication** - Point to detailed docs
3. **Pattern-based** - Teach AI to discover current state
4. **Hierarchical** - System → Shared → Service → Details
5. **Just-in-time** - Load details only when needed

## Comparison Table

| Aspect | Pattern-Based | Traditional |
|--------|---------------|-------------|
| Initial context | ~5,000 tokens | ~25,000 tokens |
| CLAUDE.md size | ~150 lines each | ~800 lines |
| Maintainability | High (thin files) | Low (one huge file) |
| Scalability | Excellent (works at any scale) | Poor (grows linearly) |
| AI focus | High (only relevant context) | Low (diluted attention) |
| Discovery | Commands + references | Hardcoded lists |
| Survives refactoring | Yes (pattern-based) | No (breaks on changes) |

## References

- Implementation: [orchestration/CLAUDE.md](https://github.com/budgetanalyzer/orchestration/blob/main/CLAUDE.md)
- Decision: [docs/decisions/003-pattern-based-claude-files.md](https://github.com/budgetanalyzer/orchestration/blob/main/docs/decisions/003-pattern-based-claude-files.md)
- Pattern: [patterns/context-window-driven-design.md](../patterns/context-window-driven-design.md)
