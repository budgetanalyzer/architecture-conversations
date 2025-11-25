# CLAUDE.md Is Not Documentation

It's a protocol for externalizing expert cognition.

---

## The Problem

You have 20 years of experience. You know how to architect systems. But in an interview, they ask you to invert a binary tree.

Your value isn't in memorized facts. It's in *how you think* - the mental models, the tradeoffs you recognize, the patterns you reach for. That can't be demonstrated on a whiteboard.

## The Insight

CLAUDE.md files aren't documentation for humans. They're instructions for AI cognition - what to attend to, how to navigate, where the boundaries are.

When you write a CLAUDE.md file, you're not explaining code. You're externalizing your thinking into a form that AI can load, interpret, and apply.

The code is incidental. The thinking is the artifact.

## What This Means

**Your expertise becomes addressable.**

A URL pointing to your CLAUDE.md file is a URL pointing to your mental model. Any AI agent that loads it starts thinking the way you encoded. Not because it was trained on your code - because your thinking is now in context.

**Your expertise becomes transferable.**

Someone else points their AI at your CLAUDE.md. Now their AI has your architectural patterns, your decision frameworks, your domain knowledge. The collaboration continues without you present.

**Your expertise becomes composable.**

Multiple experts externalize their thinking. Cross-references create edges. The result is a navigable graph of expertise that any AI can traverse.

## The Proof

I built a system called Budget Analyzer. 8 microservices. Each with a CLAUDE.md file.

But the system isn't the point. The system is proof that the process works.

The CLAUDE.md files contain my architectural thinking:
- Why context windows align with service boundaries
- Why thin documentation with discovery patterns beats comprehensive docs
- Why BFF + API gateway instead of direct service calls
- How to approach debugging when AI theorizes instead of observing

When you load this context, Claude doesn't give generic answers. It gives answers that reflect how I think about systems.

That's not documentation. That's externalized cognition.

## How To Experience It

```bash
git clone https://github.com/budgetanalyzer/architecture-conversations
cd architecture-conversations
claude
```

Then ask: "What tradeoffs did the architect make in this system, and why?"

The answer will show you whether my thinking transferred.

Or read the conversations: [github.com/budgetanalyzer/architecture-conversations/tree/main/conversations](https://github.com/budgetanalyzer/architecture-conversations/tree/main/conversations)

They're transcripts of an architect working with AI, discovering this pattern as it emerged.

## Who This Is For

**If you're an expert whose value doesn't fit in interviews** - this is how you make your thinking visible.

**If you're an architect who knows AI changes things but isn't sure how** - this is one answer: design for context windows, not just for deployment.

**If you're building systems with AI assistance** - this is how you get coherent output instead of generic suggestions.

**If you think "AI is just autocomplete"** - load this context and see what happens.

## Who This Is Not For

If you want a tutorial on how to code, this isn't it.

If you want a product to use, this isn't it.

If you want someone to explain why this matters, I can't help you. Either you recognize it or you don't.

## The Invitation

I'm looking for other experts who want to externalize their cognition.

Not to build a product. Not to start a company. Just to prove that this pattern works across domains.

If you're a driver developer, a medical diagnostician, a legal analyst, a financial modeler - anyone with tacit knowledge that resists documentation - I want to see if this works for you too.

Point your AI at my CLAUDE.md. Then build your own. Then we compare.

---

**Repository:** [github.com/budgetanalyzer/architecture-conversations](https://github.com/budgetanalyzer/architecture-conversations)

**The conversations that led here:**
- [Microservices and Context Windows](https://github.com/budgetanalyzer/architecture-conversations/blob/main/conversations/001-microservices-context-windows.md)
- [Code as AI Substrate](https://github.com/budgetanalyzer/architecture-conversations/blob/main/conversations/002-code-as-ai-substrate.md)
- [Externalized Cognition](https://github.com/budgetanalyzer/architecture-conversations/blob/main/conversations/003-externalized-cognition.md)

**Contact:** [your contact method here]

---

*This document was written by Claude, guided by Bleu Rubin. That's the point.*
