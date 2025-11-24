# Contributing to AI-Native Architecture Conversations

Thank you for your interest in contributing! This repository welcomes **architect-level discourse** about building production systems with AI assistance.

---

## What We're Looking For

### ✅ Welcome Contributions

- **Deep technical conversations** about architectural decisions
- **Production experience** with AI-assisted development
- **Patterns extracted** from real projects
- **Honest tradeoff analysis** (not promotional content)
- **Visual diagrams** that clarify relationships
- **Critiques and counter-arguments** to existing patterns

### ❌ Not Accepting

- Beginner tutorials (there are better places for these)
- Generic AI advice not specific to architecture
- Promotional content or vendor pitches
- Code samples without architectural context
- Opinions without production evidence

---

## Content Types

### 1. Conversations

**Purpose**: Deep technical discussions between architect and AI

**Format**:
```markdown
# [Topic]: [Specific Question or Insight]

**Date:** YYYY-MM-DD
**Participants:** [Your name], Claude [version]
**Context:** What prompted this conversation

## The Insight
[2-3 sentence core realization]

## The Conversation
[Full dialogue with context]

## The Pattern
[Extracted reusable concept]

## Implications for Architecture
[Practical consequences]

## Production Evidence
[Real examples from your project]

## Questions This Raises
[Follow-up explorations]

## References
- Implementation: [link to code]
- Pattern: [link to extracted pattern if applicable]
```

**Filename**: `conversations/NNN-topic-name.md` (next number in sequence)

**Length**: 2000-5000 words

**Style**: Authentic dialogue, not polished prose. Show the thinking process.

### 2. Patterns

**Purpose**: Reusable architectural concepts

**Format**:
```markdown
# Pattern: [Name]

**Status**: [Active|Experimental|Deprecated]
**Origin**: [Link to conversation]
**First Observed**: [Date, Project]

## Problem
[What problem does this solve?]

## Solution
[How does it solve it?]

## Example
[Concrete implementation]

## Benefits
[Why use this?]

## When to Use
[Strong indicators]

## When NOT to Use
[Warning signs]

## Trade-Offs
[Honest pros and cons]

## Real-World Evidence
[Production data, observations]
```

**Filename**: `patterns/pattern-name.md`

**Length**: 1000-3000 words

**Style**: Problem/Solution format with tradeoff analysis

### 3. Insights

**Purpose**: Bite-sized observations

**Format**:
```markdown
# [Topic]

[Key observation in 1-2 paragraphs]

## Example
[Concrete illustration]

## References
- [Link to deeper conversation/pattern]
```

**Filename**: `insights/topic-name.md`

**Length**: 200-500 words

**Style**: Punchy, memorable, actionable

### 4. Visuals

**Purpose**: Diagrams showing relationships and flows

**Preferred**: ASCII art (git-friendly, diff-able)

**When needed**: SVG for complex visualizations

**Requirements**:
- Clear labels
- Legend/key explaining symbols
- Description in accompanying markdown or comments

**Filename**: `visuals/diagram-name.md` or `visuals/diagram-name.svg`

---

## Contribution Process

### Step 1: Start a Discussion

Before creating content, open a GitHub Discussion:
- Outline your topic
- Share production context
- Get feedback from community

This ensures:
- Topic fits repository scope
- No duplication of existing content
- Community interest exists

### Step 2: Create Content

Use templates from this guide or existing content as examples.

**Key requirements**:
- Ground insights in production experience
- Provide concrete examples (preferably with code links)
- Analyze tradeoffs honestly
- Reference related patterns/conversations
- Maintain architect-level depth

### Step 3: Submit Pull Request

**PR Checklist**:
- [ ] Content follows template for its type
- [ ] Production evidence provided
- [ ] Cross-references to related content added
- [ ] Filename follows naming convention
- [ ] Markdown properly formatted
- [ ] No promotional content
- [ ] Tradeoffs honestly analyzed

**PR Description Should Include**:
- Type of content (Conversation/Pattern/Insight/Visual)
- Production context (what project/company/team)
- Key insight summary
- Related content links

### Step 4: Review and Discussion

Maintainers and community will:
- Ask clarifying questions
- Suggest improvements
- Validate production claims
- Ensure architectural depth

**We're looking for quality over speed.** Expect thoughtful review.

---

## Style Guidelines

### Voice and Tone

- **Honest**: Share what worked AND what didn't
- **Technical**: Assume reader is experienced architect
- **Specific**: Name technologies, show code, cite numbers
- **Humble**: Acknowledge limitations and unknowns
- **Conversational**: Write like you're explaining to a colleague

### What to Avoid

- ❌ Marketing speak ("revolutionary", "game-changing")
- ❌ Unsubstantiated claims ("this is the best way")
- ❌ Generic advice ("use microservices because scalability")
- ❌ Beginner explanations ("REST is when you...")
- ❌ Code dumps without context

### What to Embrace

- ✅ Uncertainty ("we're still learning if...")
- ✅ Tradeoffs ("this improved X but cost us Y")
- ✅ Specific numbers ("reduced context from 25k to 5k tokens")
- ✅ Counter-examples ("this doesn't work when...")
- ✅ Production war stories ("we discovered that...")

---

## Cross-Referencing

### Linking to Budget Analyzer

When referencing implementation:
```markdown
Implementation: [transaction-service/CLAUDE.md](https://github.com/budgetanalyzer/transaction-service/blob/main/CLAUDE.md)
```

### Linking Within This Repo

```markdown
Conversation: [conversations/001-microservices-context-windows.md](../conversations/001-microservices-context-windows.md)

Pattern: [patterns/context-window-driven-design.md](../patterns/context-window-driven-design.md)

Visual: [visuals/ecosystem-overview.md](../visuals/ecosystem-overview.md)
```

### Bidirectional Links

When adding content:
1. Link forward (conversation → pattern)
2. Link backward (pattern → originating conversation)
3. Create network of related content

---

## Production Evidence Standards

### What Counts as Evidence

**Strong Evidence**:
- Metrics from production system ("reduced tokens by 80%")
- Before/after comparisons with data
- Links to actual code implementing pattern
- Team observations over months
- A/B test results

**Weak Evidence**:
- "I think this works better"
- "Everyone knows that..."
- Theoretical analysis without implementation
- Single anecdote without validation

### How to Provide Evidence

**Good**:
```markdown
## Real-World Evidence

Budget Analyzer (Nov 2024):
- 8 microservices, each with CLAUDE.md
- Average file size: 150 lines
- Context reduction: 80% (5k vs 25k tokens)
- Maintainability: CLAUDE.md updates take <5 minutes
- Team size: 1-2 developers managing all services

See: [orchestration/CLAUDE.md](https://github.com/budgetanalyzer/orchestration/blob/main/CLAUDE.md)
```

**Not Good**:
```markdown
## Real-World Evidence

This pattern works really well in production. Everyone on my team loves it and we've seen huge improvements.
```

---

## Visual Design Guidelines

### ASCII Art Best Practices

**✅ Good ASCII Art**:
```
┌────────────────┐
│  Clear boxes   │
│  with labels   │
└────────┬───────┘
         │ Labeled arrows
         ▼
┌────────────────┐
│  Next step     │
└────────────────┘

KEY:
→ = Data flow
★ = Important
```

**❌ Poor ASCII Art**:
```
stuff -> thing
  |
  v
other stuff
```

### When to Use SVG

- Complex multi-layer diagrams
- Many interconnected components
- Color coding adds clarity
- Network topology diagrams

**Requirements for SVG**:
- Include source (Mermaid, Draw.io, etc.)
- Provide text description for accessibility
- Keep file size reasonable (<500KB)

---

## Review Criteria

Maintainers evaluate contributions on:

### 1. Architectural Depth (Required)

- Does this address production concerns?
- Is it beyond "hello world" level?
- Would experienced architects find value?

### 2. Production Grounding (Required)

- Is there real-world evidence?
- Can claims be verified?
- Is there honest tradeoff analysis?

### 3. Clarity (Required)

- Is the writing clear and well-organized?
- Are examples concrete and specific?
- Are diagrams helpful (not just decorative)?

### 4. Uniqueness (Preferred)

- Does this add new insights?
- Is it different from existing content?
- Does it expand the conversation?

### 5. Cross-References (Preferred)

- Links to implementations?
- References to related patterns?
- Part of content network?

---

## Code of Conduct

### Expected Behavior

- **Respectful discourse** - Disagree with ideas, not people
- **Good faith** - Assume best intentions
- **Evidence-based** - Support claims with data
- **Collaborative** - Help improve others' contributions
- **Humble** - We're all learning

### Unacceptable Behavior

- Personal attacks or insults
- Dismissive or condescending language
- Marketing or self-promotion
- Plagiarism or misrepresentation
- Harassment of any kind

**Violations**: Reported to maintainers, may result in ban.

---

## Questions?

**Before contributing**, please:

1. Read existing content to understand tone and depth
2. Open a Discussion to validate your topic
3. Review this guide thoroughly

**Need help?**
- GitHub Discussions: Ask questions about contributing
- GitHub Issues: Report bugs or suggest improvements to this guide

---

## Recognition

Contributors are recognized:
- Listed in pattern/conversation metadata
- Acknowledged in README
- Part of growing AI-native architecture community

Your production insights help shape how software is built with AI. Thank you for contributing!

---

## Templates

### Conversation Template

```markdown
# [Topic]: [Specific Insight]

**Date:** YYYY-MM-DD
**Participants:** [Your name], Claude [version]
**Context:** [What prompted this]

## The Insight

[2-3 sentences]

## The Conversation

[Full dialogue]

## The Pattern

[If extractable]

## Implications for Architecture

[Practical consequences]

## Production Evidence

[Real data from your project]

## Questions This Raises

[Follow-ups]

## References

- Implementation: [link]
- Related: [link]
```

### Pattern Template

```markdown
# Pattern: [Name]

**Status**: Active
**Origin**: [Link to conversation]
**First Observed**: [Date, Project]

## Problem

[What problem?]

## Solution

[How solved?]

## Example

[Concrete implementation]

## Benefits

[Why use?]

## When to Use

[Indicators]

## When NOT to Use

[Warning signs]

## Trade-Offs

[Pros and cons]

## Real-World Evidence

[Production data]
```

---

**Last Updated**: November 24, 2024

**Maintainers**: [List will grow as community develops]
