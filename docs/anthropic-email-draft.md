# Email to Anthropic - Final Draft

**Subject:** Externalized cognition protocol — 42 conversations documenting behavioral programming through prose

---

I've documented 42 conversations exploring CLAUDE.md files as a protocol for behavioral programming in natural language. The repository is public: github.com/budgetanalyzer/architecture-conversations

I'm not looking for employment or funding. I want to know if what I found is useful to your research.

---

## Technical Claims

**1. Sufficiently constrained prose overrides LLM failure modes.**

Forcing externalization of intermediate state converts tasks from pattern-matching (where LLMs fail) to sequential processing (where they succeed).

Demonstrated on:
- Character counting — 10/10 correct with forced decomposition procedure
- Bracket matching — state tracking across nested structures
- Multi-digit arithmetic — multiplication/division with carries and partial products

The key instruction: "Your instinct will be to 'just know' the answer. **This is how you get it wrong.**"

This isn't chain-of-thought. Chain-of-thought asks the model to "think step by step." This specifies *exactly what to write* at each step, leaving no room for pattern-matching shortcuts. The written trace is the proof of execution.

**2. Three levels of externalization emerged:**

- **Procedural**: Step-by-step overrides (guarantees correctness)
- **Heuristic**: Expert shortcuts with verification (propose → verify)
- **Framing**: Attention direction — what to notice, not what to do

The conversations trace how each level was discovered through iteration.

**3. Context degradation drives the autonomous execution loop:**

```
Goal + Success Criteria + Sandboxed Execution + Fresh Sessions
```

After ~15% of context is consumed by failed attempts, those failures become attractors. Fresh sessions with updated constraints outperform debugging in degraded context. This pattern built a working Kubernetes deployment autonomously.

**4. CLAUDE.md files are programs.**

Natural language syntax. LLM inference as runtime. Context window as execution environment. Human-auditable by design.

The distinction from "prompting": prompts are disposable and per-session. These are versioned, URI-addressable, hierarchical, composable across repositories, and specify behavior rather than hope for it.

---

## The Dual-Use Problem

The same protocol that externalizes a physicist's intuition into AI-operationalizable form can externalize a scammer's playbook. I've thought about this. Conversations 016-017 document that reflection explicitly.

I chose to open source with documented intent. The reasoning: I can't be the gatekeeper, the pattern will be discovered independently, and documented ethics alongside technique shapes how it's understood. Whether that's the right call, I don't know. But I wanted the ethical consideration visible, not hidden.

---

## What I'm Asking

30 minutes with someone who can evaluate:

1. Is the externalization-as-override pattern genuinely novel, or obvious in retrospect? (I tried to be honest about this in conversation 011, "Grounding the Abstract")
2. Does the "natural language as programming language" framing have research implications?
3. Is there connection to work you're already doing?

The repo is the evidence. Start at conversations/INDEX.md for 2-3 line summaries of all 42 conversations. The system demonstrates itself — point Claude at the CLAUDE.md file in any repo and watch the pattern execute.

---

**Links:**

- Index: github.com/budgetanalyzer/architecture-conversations/blob/main/conversations/INDEX.md
- Core insight (003): externalized cognition as protocol
- Behavioral override proof (021): self-programming via prose
- Synthesis (029): natural language programming
- Ethics reflection (016-017): the weight of what we built

---

Bleu Rubin
github.com/budgetanalyzer

---

## Notes for Bleu

**Verified email addresses (2025-11-27):**
- **sales@anthropic.com** — Best fit. Handles "partnerships" and research collaborations. UChicago/BFI partnership went through this channel.
- privacy@anthropic.com — Data requests only
- security@anthropic.com — Vulnerability reports only
- usersafety@anthropic.com — Claude behavior reports only
- support.anthropic.com — General support portal

**Note:** research@anthropic.com does not exist. sales@ is the right channel for mutual-benefit research proposals.

**Alternative routes:**
- LinkedIn messages to researchers in developer relations or alignment research (less credential-filtered)
- Anthropic Discord or community channels
- Twitter/X engagement with Anthropic researchers

**What makes this different from "I found a cool prompting trick":**
- 42 documented conversations showing the emergence, not just the conclusion
- Reproducible experiments with concrete success/failure metrics
- Self-critical (conversation 011 explicitly asks "is this just obvious?")
- Open source with full context (anyone can verify)

**The honest limitation to acknowledge:**
- ChatGPT and Gemini can already do the arithmetic (discovered in conv 028)
- Many insights may be "good practice, named" rather than novel discovery
- The value might be in the documentation/archaeology, not the techniques themselves

**Key differentiator:**
The repo isn't saying "here's a trick." It's saying "here's a protocol for externalized cognition, documented as it was discovered, with the failures and dead ends included." That's the thing that's hard to fake.
