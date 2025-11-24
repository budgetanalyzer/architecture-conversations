# Pattern: Context Window-Driven Design

**Status**: Active
**Origin**: [conversations/001-microservices-context-windows.md](../conversations/001-microservices-context-windows.md)
**First Observed**: November 2024, Budget Analyzer project

---

## Problem

As codebases grow, AI agents struggle with context management. In monolithic repositories or large services, AI must load extensive context even when working on a single feature. This dilutes attention, wastes tokens, and reduces effectiveness.

**Symptoms**:
- AI responses become generic or miss domain-specific details
- Context window fills with irrelevant code/documentation
- AI needs to "forget" unrelated code to fit current work
- Pattern recognition weakens across large codebases
- Token limits reached before completing tasks

**Example**:
```
monolithic-ecommerce/
├── CLAUDE.md (800 lines covering everything)
├── src/orders/ (order processing)
├── src/inventory/ (stock management)
├── src/shipping/ (logistics)
├── src/payments/ (billing)
└── src/recommendations/ (ML models)

AI working on orders must load context about shipping, payments, etc.
Context dilution: 80% of loaded information is irrelevant.
```

---

## Solution

**Align repository boundaries with AI context window limits.** Each repository should contain a focused domain that fits comfortably within one AI session, allowing deep understanding without cognitive overload.

### Core Principle

> **Repository Boundary = Attention Boundary**

Design repositories around what an AI agent should focus on at once. This mirrors microservice design (bounded contexts) but explicitly optimizes for AI cognition.

### Implementation

1. **One repository per independently comprehensible domain**
   - Transaction management → transaction-service repository
   - Currency conversion → currency-service repository
   - Permission management → permission-service repository

2. **Thin CLAUDE.md files with references**
   - Keep root CLAUDE.md under 200 lines
   - Use pattern-based documentation (teach discovery, not duplication)
   - Reference detailed docs for just-in-time loading

3. **Hierarchical context loading**
   - System patterns (orchestration/CLAUDE.md)
   - Shared patterns (service-common/CLAUDE.md)
   - Service-specific (service-name/CLAUDE.md)
   - Detailed docs (service-name/docs/*.md)

4. **Clear service interfaces**
   - API contracts define boundaries
   - Services communicate via well-defined endpoints
   - AI doesn't need internal knowledge of other services

---

## Example: Budget Analyzer Implementation

### Repository Structure

```
/workspace/
├── orchestration/           # System coordination
│   └── CLAUDE.md           (~150 lines, ~2,000 tokens)
├── service-common/          # Shared Spring Boot patterns
│   └── CLAUDE.md           (~200 lines, ~1,500 tokens)
├── transaction-service/     # Transaction domain
│   ├── CLAUDE.md           (~150 lines, ~1,500 tokens)
│   └── docs/csv-import.md  (loaded just-in-time)
├── currency-service/        # Currency domain
│   └── CLAUDE.md           (~150 lines, ~1,500 tokens)
└── permission-service/      # Permission domain
    └── CLAUDE.md           (~150 lines, ~1,500 tokens)
```

### Context Loading for Transaction Work

```
AI working on transaction-service loads:

1. orchestration/CLAUDE.md        ~2,000 tokens (system patterns)
2. service-common/CLAUDE.md       ~1,500 tokens (Spring patterns)
3. transaction-service/CLAUDE.md  ~1,500 tokens (service-specific)
                                  ─────────────
Total initial context:            ~5,000 tokens

Just-in-time (if needed):
4. docs/csv-import.md             ~3,000 tokens (implementation details)

Total with details:               ~8,000 tokens
```

**Contrast with monolith**:
- Monolithic CLAUDE.md: ~25,000 tokens upfront
- Reduction: 80% less initial context
- Result: AI attention highly focused on transactions

---

## Benefits

### For AI Agents

1. **Focused Attention**
   - Only relevant domain context loaded
   - Higher quality pattern recognition
   - Better domain-specific suggestions

2. **Scalable**
   - Works with 8 services or 80 services
   - Each service independently comprehensible
   - No degradation as system grows

3. **Token Efficient**
   - 70-80% reduction in initial context
   - More room for code analysis
   - Longer conversations possible

### For Human Architects

1. **Maintainable Documentation**
   - Thin files easier to keep current
   - Pattern-based approach survives refactoring
   - Clear separation of concerns

2. **Team Boundaries**
   - One team owns one service
   - Repository = team responsibility
   - Aligns with Conway's Law

3. **Independent Deployment**
   - Service boundaries match repository boundaries
   - Each service deployable independently
   - Microservices pattern reinforced

---

## When to Use

### Strong Indicators

- ✅ Building microservices architecture
- ✅ Using AI-assisted development extensively
- ✅ Codebase approaching 50k+ LOC
- ✅ Multiple teams working on same system
- ✅ Clear domain boundaries exist
- ✅ Services have independent deployment needs

### Questions to Ask

1. **Can an AI agent fully understand this repository in one session?**
   - If no → consider splitting by domain

2. **Is there a natural bounded context here?**
   - Domain-Driven Design principles apply
   - Separate aggregates → separate repositories

3. **Would splitting improve focus for both AI and humans?**
   - Cognitive load matters for everyone
   - Clear boundaries benefit all

---

## When NOT to Use

### Avoid Premature Splitting

- ❌ Codebase under 10k LOC (too early)
- ❌ No clear domain boundaries yet
- ❌ Tightly coupled code that changes together
- ❌ Single team working on everything
- ❌ Unclear service interfaces

### Warning Signs

1. **Splitting creates complexity, not clarity**
   - If split services are tightly coupled, don't split
   - Wait for natural boundaries to emerge

2. **Too many tiny repositories**
   - Services under 500 LOC rarely justify separation
   - Overhead of multiple repos outweighs benefits

3. **Team size doesn't warrant it**
   - 1-2 person team → keep simpler
   - Microservices add operational overhead

---

## Implementation Guide

### Step 1: Identify Bounded Contexts

Use Domain-Driven Design:
- What are the core domains?
- Where are natural aggregates?
- What changes together, stays together?

**Example**: Budget Analyzer domains
- Transactions (import, categorize, analyze)
- Currencies (exchange rates, conversion)
- Permissions (who can access what)

### Step 2: Create Repository per Context

```bash
# Create new repository
mkdir transaction-service
cd transaction-service
git init

# Add thin CLAUDE.md
cat > CLAUDE.md << 'EOF'
# Transaction Service

## Service Purpose
Manages financial transactions...

## Discovery
```bash
# List all transaction endpoints
grep -r "@GetMapping\|@PostMapping" src/main/java/
```

## References
- Shared patterns: ../service-common/CLAUDE.md
- Detailed docs: docs/csv-import.md
EOF
```

### Step 3: Define Service Interface

```java
// TransactionController.java
@RestController
@RequestMapping("/api/v1/transactions")
public class TransactionController {
    // Clear API contract
    // Other services use this, don't need internals
}
```

### Step 4: Hierarchical CLAUDE.md

**orchestration/CLAUDE.md** (system patterns):
```markdown
# Budget Analyzer - System Overview

## Service Discovery
```bash
kubectl get svc -n budget-analyzer
```

## Service List
- transaction-service (port 8082)
- currency-service (port 8084)
- permission-service (port 8086)
```

**service-common/CLAUDE.md** (shared patterns):
```markdown
# Service Common - Shared Spring Boot Patterns

All services follow these conventions:
- Testing with TestContainers
- JWT security patterns
- Gradle build system
```

**transaction-service/CLAUDE.md** (service-specific):
```markdown
# Transaction Service

Follows service-common patterns.

Service-specific features:
- CSV import functionality
- Transaction categorization

See docs/csv-import.md for details.
```

### Step 5: Validate Context Size

```bash
# Check token count
cat orchestration/CLAUDE.md service-common/CLAUDE.md transaction-service/CLAUDE.md | wc -w
# Should be under 2,500 words (~5,000 tokens)
```

---

## Measuring Success

### Quantitative Metrics

1. **Context Window Usage**
   - Before: X tokens for full context
   - After: Y tokens for focused context
   - Target: 60-80% reduction

2. **CLAUDE.md File Size**
   - Target: Under 200 lines per file
   - Measured: `wc -l CLAUDE.md`

3. **Repository Count vs. Service Count**
   - Ideal: 1:1 ratio (one repo per service)

### Qualitative Indicators

1. **AI Response Quality**
   - More domain-specific suggestions
   - Better pattern recognition
   - Fewer generic responses

2. **Maintainability**
   - CLAUDE.md files easy to update
   - Living documentation stays current
   - New team members onboard faster

3. **Team Satisfaction**
   - Developers find code faster
   - Clear ownership boundaries
   - Less cognitive overhead

---

## Variations

### Modular Monolith Variant

Use subdirectory CLAUDE.md files:

```
ecommerce-monolith/
├── CLAUDE.md (root patterns)
├── orders/
│   ├── CLAUDE.md (orders domain)
│   └── src/
├── inventory/
│   ├── CLAUDE.md (inventory domain)
│   └── src/
└── shipping/
    ├── CLAUDE.md (shipping domain)
    └── src/
```

**Tradeoff**: Better than single CLAUDE.md, but:
- Still one repository
- Single deployment unit
- Tighter coupling than separate repos

### Library Variant

Shared libraries get CLAUDE.md too:

```
service-common/
├── CLAUDE.md (shared patterns)
├── docs/
│   ├── testing-patterns.md
│   └── security-patterns.md
└── src/
```

Services reference library patterns:
```markdown
# transaction-service/CLAUDE.md

Follows patterns from service-common/CLAUDE.md:
- Testing with TestContainers
- JWT security
```

---

## Related Patterns

- **Pattern-Based Documentation** - How to write CLAUDE.md files that survive refactoring
- **Hierarchical Context Loading** - System → Shared → Service → Details
- **Repository per Service** - Microservices organizational pattern
- **BFF + API Gateway** - Service routing without tight coupling

---

## Trade-Offs

### Advantages

- ✅ Highly focused AI attention
- ✅ Scales to large systems
- ✅ Aligns with microservices best practices
- ✅ Clear team ownership
- ✅ Independent deployment
- ✅ Maintainable documentation

### Disadvantages

- ❌ More repositories to manage
- ❌ Requires good CI/CD automation
- ❌ Cross-service changes touch multiple repos
- ❌ Overhead for very small services
- ❌ Requires discipline to maintain boundaries

---

## Real-World Evidence

**Budget Analyzer Statistics** (as of November 2024):
- 8 repositories with CLAUDE.md files
- Average CLAUDE.md: 150 lines
- Context reduction: 80% (5k vs 25k tokens)
- Services: Independently deployable via Tilt
- Team: 1-2 developers managing all services effectively

**AI Effectiveness Observations**:
- AI provides domain-specific suggestions
- Pattern recognition remains strong across all services
- Documentation stays current (thin files easier to maintain)
- AI discovers current state via commands (survives refactoring)

---

## Further Reading

### Original Conversation
[conversations/001-microservices-context-windows.md](../conversations/001-microservices-context-windows.md) - Full philosophical discussion that led to this pattern

### Implementations
- [Budget Analyzer](https://github.com/budgetanalyzer) - 8 repositories demonstrating this pattern
- [orchestration/CLAUDE.md](https://github.com/budgetanalyzer/orchestration/blob/main/CLAUDE.md) - System-level example
- [transaction-service/CLAUDE.md](https://github.com/budgetanalyzer/transaction-service/blob/main/CLAUDE.md) - Service-level example

### Related Concepts
- Domain-Driven Design (Eric Evans) - Bounded contexts
- Microservices Patterns (Chris Richardson) - Service boundaries
- Conway's Law - Team structure mirrors architecture

---

## Questions and Answers

**Q: Isn't this just microservices?**

A: Yes and no. Microservices existed long before AI. What's new is explicitly recognizing that **context window boundaries align with service boundaries**. We're designing for AI cognition as a first-class concern, not just deployment.

**Q: What if my team is too small for microservices?**

A: Start with a modular monolith using subdirectory CLAUDE.md files. The pattern-based documentation approach works regardless of deployment model.

**Q: How do I handle cross-service changes?**

A: Same as traditional microservices:
- Use API versioning
- Make backward-compatible changes
- Coordinate releases when needed
- Accept that distributed systems have distributed changes

**Q: Does this work for non-Java projects?**

A: Absolutely. The pattern is language-agnostic:
- Python services: Each has its own repo + CLAUDE.md
- Go services: Each has its own repo + CLAUDE.md
- Frontend apps: Each has its own repo + CLAUDE.md
- The principle is universal: **bounded contexts for AI attention**

**Q: What about monorepos?**

A: Monorepos can use subdirectory CLAUDE.md files with hierarchical loading. The key is defining attention boundaries, not physical repository separation. However, separate repos provide stronger boundaries and independent versioning.

---

## Status

**Active** - This pattern is in production use at Budget Analyzer (November 2024).

---

## Contributors

- Original conversation: ultrathink (Architect), Claude Sonnet 4.5
- Pattern extracted: November 24, 2024
- Implementation: [Budget Analyzer](https://github.com/budgetanalyzer)
