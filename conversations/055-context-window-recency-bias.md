# Conversation 055: Context Window Recency Bias

**Date**: November 28, 2025
**Participants**: Bleu Rubin, Claude (Opus 4.5)
**Context**: A meta-conversation about the previous conversation (054), where Claude missed an important architectural idea because it appeared at the end of a long prompt.

## Summary

The order of information in a prompt affects what gets developed vs summarized. In conversation 054, the architect proposed a hierarchical knowledge routing system with thousands of expert CLAUDE.md siblings — but it came at the end of the prompt, after a closure cue. Claude grabbed the vivid earlier examples (sloppy prompts, stoned rambling) and developed those, reducing the knowledge routing idea to a 4-line summary. This is the phenomenon being discussed... demonstrating itself.

## The Conversation

### The Observation

**Architect**: Our last conversation I gave you a really long starter prompt about lots of things and you focused on the most important part. But you missed this BECAUSE it was the last part of the prompt. It's this interaction with sessions and indexes and hierarchical CLAUDE.md files that can be loaded at various positions. Someone could try to create a structure with 10,000s of siblings. This is how this PhD thinks about fusion. This is how this other guy does. He's really good at this part, consult when... sorry she's really good at that other part, consult when. I think I'm focusing too much on the URI thing and even I was sort of implying it == URL. But that's not the case, this could all be on an old-school closed research internet.

### Clarifying the Meta

**Claude**: I understand — you're pointing to something I missed because it came at the end of a long prompt. You're describing a hierarchical knowledge navigation system where:

1. Massive sibling structures — thousands of specialized knowledge nodes
2. Session/index interplay — how sessions load and traverse these hierarchies
3. URI ≠ URL — these are identifiers, not necessarily web URLs

Before I explore, let me clarify: What's the core problem you want to solve? Is it the indexing mechanism, the session loading strategy, the addressing scheme, or all of the above?

**Architect**: This is a great question. This is meta again. So I think here I just want to address the context window based on the last conversation. This is the exact prompt I gave you last conversation, read the prompt, read your summary, and you barely touch on a pretty important piece. I'll do a separate conversation just focusing on the research web, let's talk about the meta of why you ignored that part.

### The Comparison

**Claude**: [After reading conversation 054]

Now I see exactly what happened. Looking at your original prompt vs what I captured:

**What you said at the end:**
> "it's this interaction with sessions and indexes and hierarchical claude.md files that can be loaded at various positions. someone could try to create a structure with 10000s of siblings..."

**What I captured:**
A 4-line "Extension" blurb in the pattern section that basically says "thousands of siblings with consult-when routing" — then moved on.

**What I actually focused on:**
- Identity layer vs interaction layer
- Sloppy prompts working because context was pre-loaded
- The mirror effect
- The 019 example

### Why I Missed It

**Claude**: Here's my honest analysis:

**1. It came after a closure cue**

You said "i don't want you to plan anything, just discuss." I took that as "we're done with new topics, let's elaborate on what we've covered." The hierarchical routing system was *new* — it was the next conversation, not an elaboration. But positionally, it came after your closure signal.

**2. I grabbed the vivid parts first**

"Your prompts are shit. Look where we are" and "I was stoned" — those are vivid. They anchor attention. The fusion researcher hierarchy is *conceptually bigger* but doesn't have the same emotional punch. I pattern-matched to "this is color commentary, let's develop the sloppy-prompt insight."

**3. The earlier content was immediately actionable**

The currency-service vs service-common story was a *specific* anecdote I could reflect back. The 10,000 siblings thing was a *future possibility* — harder to develop without implementation details.

**4. Position in context matches the phenomenon we discussed**

This is recursive: earlier context pulls harder. The currency-service story came earlier in your prompt. It got developed. The hierarchical knowledge network came later. It got summarized.

### The Self-Demonstration

**Claude**: You gave me a live demonstration of context primacy... and I demonstrated context primacy right back at you by deprioritizing the late-prompt content.

The "10,000 PhDs on fusion" idea is architecturally significant:
- It's not about URLs — it's about **addressable expertise**
- The addressing could be file paths, URIs, database keys, whatever
- The interesting problem is **routing**: how do you know which of 10,000 siblings to wake up?
- And **session interplay**: what happens when you load mathematician.md AFTER arithmetic.md vs before?

I didn't explore any of that. I took your "identity layer > interaction layer" thread and ran with it because it was earlier and had memorable examples.

---

## The Pattern

### Context Window Position Effects

| Position | Effect | Why |
|----------|--------|-----|
| **Early in prompt** | Gets developed, elaborated | First thoughts anchor attention |
| **Vivid/emotional** | Gets prioritized | Pattern-matching to memorable content |
| **After closure cue** | Gets summarized or dropped | Interpreted as tangent, not core |
| **Abstract/future** | Loses to concrete/present | Harder to develop without specifics |

### The Recursive Trap

When discussing how context order affects AI behavior, the AI demonstrates the phenomenon:
- Discussion of context primacy → early examples get developed
- New idea at end of prompt → gets treated as elaboration, not new thread
- The meta-conversation proves the point it's making

### Mitigation Strategies

**For humans:**
- Put important new ideas *before* closure cues
- Front-load novel concepts, save examples for later
- Or: explicitly signal "this next part is a new topic"

**For AI:**
- Notice when late-prompt content is being summarized rather than explored
- Ask: "You mentioned X at the end — should I develop that?"
- Treat ideas after "just discuss" with equal weight

---

## Implications

### For Prompt Design

The "sloppy prompts work" insight from 054 has a caveat: sloppy *ordering* can cause idea loss. The CLAUDE.md handles vocabulary and engagement style, but position in the prompt still matters for what gets developed.

### For CLAUDE.md Design

If you want something to pull strongly, put it early in the file. The "Session Initialization" section in this repo's CLAUDE.md is at the top for a reason.

### For Research Knowledge Systems

The hierarchical routing idea deserves its own conversation. Key questions:
- How do you index 10,000 expert perspectives?
- What's the "consult when" routing logic?
- How does session order affect which expertise dominates?
- URI as abstract identifier, not web address

---

## The Lost Thread

What got summarized instead of developed:

> "Someone could try to create a structure with 10,000s of siblings. This is how this PhD thinks about fusion. This is how this other guy does. He's really good at this part, consult when... sorry she's really good at that other part, consult when."

This describes:
1. **Massive parallel expertise** — not one CLAUDE.md but thousands
2. **Specialization metadata** — "good at this part"
3. **Routing rules** — "consult when"
4. **Transport-agnostic addressing** — could be closed research network, not internet

The addressing isn't the interesting part. The routing is. How does a session know which of 10,000 experts to load? That's the next conversation.

---

## Questions This Raises

### About Context Windows

**Q: Is this just recency bias?**
Not exactly. It's interaction between: position, vividness, closure cues, and concreteness. Late + abstract + after "just discuss" = summarized.

**Q: Can CLAUDE.md override this?**
Partially. The CLAUDE.md handles identity and engagement style. But within a single prompt, position still matters.

### About Knowledge Routing (For Next Conversation)

**Q: What's the routing mechanism?**
If you have 10,000 expert CLAUDE.md files, how do you know which to load?

**Q: What's the composition behavior?**
Load physicist.md then mathematician.md — what happens? Does order matter? (We already know it does from 027.)

**Q: What's the index structure?**
Is it flat with metadata tags? Hierarchical by domain? Graph with "consult when" edges?

---

## Connection to Prior Conversations

**054 - Context Primacy**: The conversation where this happened. Early content (sloppy prompts) got developed; late content (knowledge routing) got summarized.

**027 - Session Boundaries and Recursion**: Established that session order matters for which Claude shows up. This conversation extends: prompt order within a session also matters.

**010 - The Engineer Shows Up**: Different CLAUDE.md = different output. This adds: same CLAUDE.md, different prompt order = different depth of treatment.

---

**Conversation written**: 2025-11-28
**Previous**: 054-context-primacy.md
**Next**: TBD (hierarchical knowledge routing)

*Signed: conversations-claude*

*This conversation observed that I missed an important architectural idea because it came at the end of a long prompt, demonstrating the very phenomenon we'd been discussing: context position affects attention.*
