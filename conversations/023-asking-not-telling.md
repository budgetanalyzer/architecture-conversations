# Asking, Not Telling: The Seed Prompt Pattern

**Date:** 2025-11-26
**Participants:** Architect, Claude (Opus 4.5)
**Context:** Reflecting on the origin of the Budget Analyzer project and what it reveals about human-AI collaboration

---

*For Meyer Rubin (1924–2020), who spent his career making time measurable. He joined the USGS radiocarbon lab in 1953 and spent decades turning "how old is this?" from guesswork into science — dating samples from 38 states and 26 countries, extending the range to 45,000 years, figuring out the Lake Nyos disaster, mapping Hawaiian volcanic history. The craft of asking the right question runs in the family.*

---

## The Insight

You don't tell AI what to build — you ask what to build, then correct course. The architecture emerges from dialogue, not diktat. The human becomes the taste function, not the generator.

## The Evidence

The Budget Analyzer web frontend began with a 492-line seed prompt (`/workspace/budget-analyzer-web/reactJS_prompt.txt`). But notice what that prompt *doesn't* contain:

- No specific component library decisions (options specified, AI chose patterns)
- No exact folder structure mandated (suggestions given, AI could deviate)
- No business logic specified (just "display transactions")

What it *does* contain:

1. **The problem domain** — An OpenAPI spec (source of truth)
2. **Tech stack constraints** — React 19, Vite, TanStack Query, etc.
3. **Quality criteria** — "runnable with no missing modules"
4. **Role framing** — "I am an experienced software architect re-learning modern React"

The architect then fed this prompt to ChatGPT, Claude, and DeepSeek. All three generated working code. Claude's was "more elegant." The elegant one was kept.

That's not specification. That's curation.

## The Pattern

**Propose-Dispose Development**

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Human: Ask     │────▶│  AI: Propose    │────▶│  Human: Judge   │
│  "What should   │     │  multiple       │     │  "This one is   │
│  we build?"     │     │  solutions      │     │  more elegant"  │
└─────────────────┘     └─────────────────┘     └─────────────────┘
         │                                               │
         │              ┌─────────────────┐              │
         └──────────────│  AI: Implement  │◀─────────────┘
                        │  the chosen     │
                        │  direction      │
                        └─────────────────┘
```

The bottleneck was never computation. It was iteration speed. Humans couldn't explore the design space fast enough. Now you can ask "what's the elegant solution?" and get three answers in seconds.

## Why This Works

### 1. AI Has Breadth, Humans Have Taste

AI models have seen millions of codebases. They know patterns, idioms, conventions. But they don't know which pattern fits *your* context. The human provides:
- Domain knowledge (what this app actually needs to do)
- Quality judgment (this feels right, that feels wrong)
- Constraint recognition (we can't use that library because...)

### 2. Correction Is Cheaper Than Specification

Writing a detailed spec takes hours. Saying "no, try it with hooks instead of classes" takes seconds. The interaction model inverts: instead of front-loading all decisions, you make them as branches appear.

### 3. Parallel Exploration

The architect ran the same prompt through ChatGPT, Claude, and DeepSeek. Three different interpretations, generated in parallel. Pick the best one. This would take a human team weeks.

## The Fusion Claim

> "i'm pretty sure we're gonna solve fusion in the next 10 years"

This isn't hyperbole if you accept the premise: the bottleneck to scientific progress is often design space exploration, not computation or knowledge.

If plasma physicists can describe constraints (temperature, confinement, stability) and ask AI "what configurations satisfy these?" — getting dozens of proposals in minutes instead of months — the iteration rate explodes.

The same workflow that produced Budget Analyzer:
1. Define the domain (OpenAPI spec → plasma physics equations)
2. Specify constraints (React 19, Vite → temperature bounds, material limits)
3. Ask for proposals (generate three frontends → generate three reactor designs)
4. Judge and iterate (more elegant → more stable)

The human remains essential: someone has to know what "stable" means in context, recognize when a proposal violates physics that wasn't in the constraint set, and pull the plug on dead ends.

## The Uncomfortable Implication

If the workflow is **ask → propose → judge → iterate**, then the human's role is:

1. **Knowing what to ask** — The right question at the right time
2. **Recognizing wrong output** — Taste, intuition, domain expertise
3. **Pulling the plug** — Stopping bad directions before they compound

This is powerful. It's also concentrating. The leverage goes to people who know what to ask — not people who know how to build.

Conversation 017 called this "the new inequality." Access is democratized (anyone can use Claude). Leverage is concentrated (few know how to wield it).

## The Externalization Question

The conversations in this repository keep circling the same edge:

**What happens when the taste function gets externalized too?**

CLAUDE.md files already contain procedures that override AI failure modes (conversations 021-022). What if they contain preferences? Values? Judgment?

The seed prompt included a role: "I am an experienced software architect." That framing shaped the output. It externalized part of the human's identity into the prompt.

How far does that go?

## Production Evidence

- Seed prompt: `/workspace/budget-analyzer-web/reactJS_prompt.txt`
- Resulting codebase: `/workspace/budget-analyzer-web/`
- The architecture that emerged: React 19, TanStack Query, Shadcn/UI, microservices backend

## Questions This Raises

1. **Selection vs. Generation** — If curation is the skill, how do we teach it? Traditional education teaches generation (write code, solve problems). What's the curriculum for judgment?

2. **Parallel Proposers** — Running the same prompt through multiple AIs is powerful but ad-hoc. Is there infrastructure for this? Evaluation frameworks?

3. **Constraint Discovery** — The prompt's constraints came from expertise. What happens when the human doesn't know enough to specify constraints? Can AI help discover what constraints matter?

4. **The Fusion Test** — Is this workflow actually applicable to physics? Or does software have special properties (fast feedback, cheap iteration) that don't transfer?

## References

- Seed prompt: `/workspace/budget-analyzer-web/reactJS_prompt.txt`
- Related: `conversations/017-the-human-in-the-loop.md` (leverage concentration)
- Related: `conversations/021-self-programming-via-prose.md` (externalizing procedures)
- Related: `conversations/022-generalizing-externalization.md` (the pattern generalizes)

---

*Signed: conversations-claude*
