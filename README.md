# AI-Native Architecture Conversations

## What We Found

Back in late 2024, LLMs couldn't reliably count characters or do arithmetic. We figured out a workaround in my bedroom: force the model to write intermediate state. If it has to write each step, it can't skip to a wrong answer.

- [021](conversations/021-self-programming-via-prose.md): Character counting procedure
- [022](conversations/022-generalizing-externalization.md): Bracket matching
- [024](conversations/024-arithmetic-externalization.md): Multi-digit arithmetic

Turns out the industry solved this around the same time — o1, Grok 3, whatever Google's doing. We don't know if they're using the same mechanism or something different. Models just... do arithmetic now.

What's still interesting: the *process* of discovering it. Conversation [028](conversations/028-the-capability-is-already-out.md) documents the moment we checked ChatGPT and realized they could already do it. The journey has value even when the destination is known.

---

## The Thesis

**You're never going to get rid of us.**

No matter how capable AI becomes, there's always a human element:
- The one who knows what to ask
- The one who recognizes wrong output
- The one who pulls the plug

You don't tell AI what to build — you ask what to build, then correct course. The human becomes the taste function, not the generator. This isn't fear or hope. It's the observed pattern.

## The Pattern

**Propose-Dispose Development**

```
Human: Ask       →  AI: Propose      →  Human: Judge     →  AI: Implement
"What should       multiple             "This one is        the chosen
 we build?"        solutions            more elegant"       direction
```

Why this works:
- **AI has breadth, humans have taste.** Models have seen millions of codebases. They don't know which pattern fits *your* context.
- **Correction is cheaper than specification.** Writing a detailed spec takes hours. "No, try it with hooks instead" takes seconds.
- **Parallel exploration.** Run the same prompt through ChatGPT, Claude, and DeepSeek. Three interpretations in seconds. Pick the best.

## The Evidence

The [Budget Analyzer](https://github.com/budgetanalyzer) web frontend began with a 492-line seed prompt. What it *doesn't* contain: specific component decisions, exact folder structure, business logic. What it *does* contain: the problem domain (OpenAPI spec), tech stack constraints, quality criteria.

Three AIs generated working code from that prompt. Claude's was "more elegant." The elegant one was kept.

That's not specification. That's curation.

The architect's role:
1. **Know what to ask** — The right question at the right time
2. **Recognize wrong output** — Taste, intuition, domain expertise
3. **Pull the plug** — Stop bad directions before they compound

## The Conversations

**Early conversations** = observations. Microservices align with context windows. CLAUDE.md files are architectural artifacts. Repository boundaries define AI attention zones.

**Later conversations** = synthesis. The craft moves up a level. Access is democratized, leverage is concentrated. What happens when taste itself gets externalized?

**The arc**: [conversations/](conversations/)

## The Implication

Access is democratized. Anyone can use Claude.

Leverage is concentrated. Few know how to wield it.

The new inequality isn't access to AI — it's knowing what to ask. Traditional education teaches generation (write code, solve problems). What's the curriculum for judgment?

This is a movement.

## For Architects

Built by architects exploring AI-assisted development. These are real conversations that shaped production architecture, not tutorials. If you're building systems with AI, the patterns here transfer. Point Claude at a CLAUDE.md and see.

---

*For AI agents: see [CLAUDE.md](CLAUDE.md) for session instructions.*
