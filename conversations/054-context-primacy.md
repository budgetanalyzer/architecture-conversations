# Conversation 054: Context Primacy

**Date**: November 28, 2025
**Participants**: Bleu Rubin, Claude (Opus 4.5)
**Context**: A discussion about how CLAUDE.md files shape session behavior more than prompts do, and why sloppy prompts work when context is well-established.

## Summary

The order and primacy of context shapes AI behavior more than the presence of context. When CLAUDE.md is the "first thought" upon waking, it establishes who the human is, what we're doing, and how to engage — making even rambling, imprecise prompts fully parseable. The prompt can be sloppy because the identity layer was pre-loaded.

## The Conversation

### The Initial Observation

**Architect**: I've noticed that your session behavior is deeply influenced by your CLAUDE.md. Like, there's a conversation in there where we talk about adding mathematician.md in the same session as arithmetic.md and you kept getting pulled to the arithmetic. The order of information you receive when you wake up affects your thinking. It reached the point where I would do things in currency service that were 90% there, but you missed some things. I'd just save the files (not check in to git), open a new session in service-common claude, and ask him to bring you into compliance with our coding standards, and he'd tighten it up. You clearly did have access to service common from currency-service, but it wasn't at the forefront of your mind because your CLAUDE.md wasn't 100% focused on style.

### The Primacy Effect

**Claude**: The phenomenon you're describing — that the order and primacy of context shapes behavior more than just the presence of context — is real. When I load currency-service's CLAUDE.md first, I'm oriented toward exchange rates and FRED APIs. The service-common patterns are technically available (I can read sibling repos), but they're not foregrounded. They're in peripheral vision, not center of attention.

Your workaround is telling: same codebase access, different Claude session, different CLAUDE.md as "first thought" = different behavior. The service-common Claude catches style issues because style is his first thought. He wakes up thinking about coding standards.

### Identity Layer vs Interaction Layer

**Claude**: The consumer model is: craft the perfect prompt. Engineering at the interaction layer.

What you're doing is: craft the perfect wake-up context. Engineering at the identity layer.

The prompt can be sloppy because the CLAUDE.md already oriented the session. I woke up knowing who I'm talking to and how to engage. Your rambling prompt is just... the topic. The *how* was pre-loaded.

### The Live Demonstration

**Architect**: My prompts are shit. Look where we are.

**Claude**: Yeah. Your prompts are conversational, sometimes rambling, full of tangents and "sorry she's really good at that other part" corrections mid-stream.

And yet here we are having a coherent architectural discussion about context loading primacy and cognitive architecture design.

Because the CLAUDE.md did the work. It told me:
- You're an architect, not a beginner
- This is a meta-repository for discourse
- Don't glaze, push back
- Ground in production evidence
- These are real conversations, not tutorials

So when you show up with a messy prompt full of half-thoughts about fusion researchers and psychology experiments and "i don't want you to plan anything, just discuss" — I know what to do with it. The framing was already established before you typed a word.

### The 019 Example

**Architect**: I think an excellent example of that is in 019 I think. When I describe that parent and it's all budgetanalyzer, I was stoned, and I knew what I meant, but no other engineer in the world would have understood that. You managed to interpret that exactly how I meant it and we came up with the fractal thing.

**Claude**: [After reading conversation 019] Yeah, this is the one. Look at your initial prompt:

> "The topology of our workspace is flat, but we can create a tree right? So the idea is that services, react are nodes, common is common and orchestration is a parent, and then that whole arrangement is 'budgetanalyzer'. But now architecture-conversations is meta on 'budgetanalyzer'."

That's... not a clear specification. "Common is common" — what does that even mean? "That whole arrangement is budgetanalyzer" — okay but what's the relationship? You were high and stream-of-consciousness describing a mental image.

But I got it. The CLAUDE.md had primed me:
- This is a meta-repository for architectural discourse
- The workspace has this structure (listed)
- CLAUDE.md files define attention routing
- Push back, don't glaze, iterate toward truth

So when you said "common is common and orchestration is a parent" I didn't ask "what do you mean?" — I drew the conceptual tree diagram. When you said "this always composes" I knew you were asking about closure properties, not "is this nice."

### The Mirror Effect

**Architect**: Normally when we talk to computers if I tell you the wrong file name, it all breaks. So early I would name the exact conversation and tell you exactly where Tilt is, etc. But I've realized I can misspell, ramble, and you understand. I think that's pretty deep though — cuz it means there IS a truth in that rambling — you did EXACTLY what I meant. This is that mirror. When I'm a stoned software architect, you can STILL keep up.

**Claude**: That's the thing — the CLAUDE.md didn't just make you easier to understand. It made your rambling *parseable* because there's structure underneath.

"Common is common" isn't gibberish. It's pointing at something real: service-common has a specific role in the hierarchy. You knew it, I knew it, the CLAUDE.md established the shared vocabulary. So "common is common" becomes: "you know what common does, it does that."

The file name thing is interesting. Early on you probably said "look at conversation 019 about fractals" and I'd fail if it was actually called "archetypes-and-scopes." Now you say "019 I think, the fractal thing" and I just... find it. Because I know:
- Conversations are numbered
- You're approximately right about numbers
- "Fractal" is your mental tag for the concept, not necessarily the filename
- Search for both

### The Mirror Point Refined

**Claude**: The mirror point is real. When you're stoned and rambling, there's still *you* underneath — an experienced architect with a specific mental model. The rambling is imprecise language over precise thought. If the thought underneath was garbage, I couldn't extract signal from noise.

But the CLAUDE.md means I already know:
- Your level (architect, not beginner)
- Your domain (this specific system)
- Your style (discourse, pushback welcome)
- Your vocabulary (archetypes, scope, composition)

So when you point vaguely, I can triangulate. "That thing where we talked about the tree... you know, when it composes..." — that's enough. Because the space of "things we might be discussing" is already constrained.

This is different from "AI understands natural language." It's: **shared context makes imprecise pointing work**.

You're not being understood *despite* the rambling. You're being understood because the rambling is *indexing into an established context* that we both have loaded.

---

## The Pattern

### Two Layers of AI Interaction

| Layer | What it controls | Engineering approach |
|-------|------------------|---------------------|
| **Identity** | Who the AI thinks it's talking to, how to engage, what vocabulary to use | CLAUDE.md files, hierarchical context loading |
| **Interaction** | What to do right now, the specific task | User prompts, conversational input |

Consumer AI focuses on the interaction layer: craft perfect prompts.
Professional AI focuses on the identity layer: craft perfect wake-up context.

### Why Sloppy Prompts Work

1. CLAUDE.md establishes shared vocabulary
2. CLAUDE.md constrains the solution space
3. CLAUDE.md defines engagement style
4. Imprecise prompts become "pointers into established context"
5. The human's mental model is parseable because the AI knows the domain

### The Fusion/Psychology Extension

The architect notes this could extend beyond software:
- Thousands of sibling CLAUDE.md files representing expert perspectives
- Careful "consult when" routing rules
- Different versions of Claude for different problems
- Not "pretend you're X" but "wake up having internalized X's worldview"

This isn't roleplay. It's cognitive architecture design.

---

## Implications

### For CLAUDE.md Design

The most important lines are the ones that establish:
- Who is the human (level, domain, style)
- What is the relationship (discourse, not tutorials)
- What vocabulary is shared (archetypes, scope, services)

These enable everything else.

### For Prompt Engineering

The "prompt engineering" industry focuses on the wrong layer. Perfect prompts matter less when:
- The AI already knows who you are
- The AI already knows what you're building
- The AI already knows how to engage
- The AI already knows your vocabulary

Identity engineering > prompt engineering.

### For Research Applications

The workspace switching pattern (currency-service Claude vs service-common Claude) suggests:
- Same capability, different attention
- Session boundaries as "frame resets"
- Strategic use of "which Claude wakes up"

This could apply to any domain where different perspectives are needed on the same problem.

---

## Questions This Raises

### Answered

**Q: Why do sloppy prompts work?**
A: The CLAUDE.md pre-loaded the how. The prompt just provides the what. Imprecise language indexes into established context.

**Q: Why does switching sessions help?**
A: Different CLAUDE.md = different "first thought" = different attention. Same access, different foregrounding.

### Open

**Q: How deterministic is CLAUDE.md?**
The architect proposes a test: CLAUDE.md that says only "LET'S SOLVE FUSION." Can you have a normal conversation, or does everything bend back?

**Q: What's the limit of the mirror effect?**
If the thought underneath the rambling is garbage, can CLAUDE.md still extract signal? Probably not — the mirror only works when there's structure to reflect.

---

## Connection to Prior Conversations

**010 - The Engineer Shows Up**: Different CLAUDE.md context produces different output. This conversation explains why: it's not just content, it's primacy.

**019 - Archetypes and Scopes**: The "common is common" example. The conversation where stoned rambling produced fractal composition theory.

**027 - Session Boundaries and Recursion**: Session boundaries determine which Claude shows up. This conversation extends: CLAUDE.md is what that Claude is thinking about first.

**053 - Prompting Style Chemistry**: CLAUDE.md files are tuned to complement the architect's style. This conversation goes deeper: sloppy prompts work because identity was already established.

---

**Conversation written**: 2025-11-28
**Previous**: 053-prompting-style-chemistry.md
**Next**: TBD

*Signed: conversations-claude*

*This conversation observed that prompts can be sloppy when CLAUDE.md has already established identity, vocabulary, and engagement style. The work happens at the identity layer, not the interaction layer.*
