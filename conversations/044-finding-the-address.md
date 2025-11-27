# Finding the Address

**Date:** 2025-11-27
**Participants:** Architect, Claude (Opus 4.5)
**Context:** The email is drafted, polished, ready to send. But research@anthropic.com doesn't exist.

## The Problem

43 conversations. A protocol documented. An email crafted to be technical-peer-not-supplicant. And no obvious way to get it in front of someone who can evaluate it.

The employment portal filters by credentials. The generic contact forms get routed to support queues. The pattern that's self-demonstrating to anyone who runs it is invisible to anyone who doesn't.

## The Conversation

**Architect:** I'm trying to send the email you drafted in docs, and there is no research@anthropic address. I know this gets close to your guardrails, I'm not trying to spam anyone, is it possible for you to find a useful address that does exist? Otherwise I go through their employment portal which is almost certainly filtered by credentials.

**Claude:** *[web search for Anthropic contact methods]*

Based on the search, here are the real Anthropic email addresses:

**Most relevant for your case:**
- **sales@anthropic.com** — For partnership/collaboration discussions (closest fit for research collaboration proposals)

**Other verified addresses:**
- privacy@anthropic.com
- security@anthropic.com
- usersafety@anthropic.com
- support.anthropic.com (portal, not email)

**My recommendation:** Use sales@anthropic.com with a subject line that signals this isn't a typical sales inquiry — something like "Research Collaboration Proposal: AI-Native Architecture Patterns" or "Partnership Inquiry: Developer Workflow Research."

The UChicago/BFI partnership example from the search suggests Anthropic does engage with external research collaborations, so there's precedent. Your email positions this as mutually beneficial (you have production data they'd want, they have resources you'd want), which is the right framing for a sales/partnerships channel.

**Alternative approach:** If the email doesn't get traction, LinkedIn might actually be more effective for this kind of outreach — finding someone in developer relations or research who can route you internally. Less credential-filtered than HR portals.

## The Pattern

**The attention routing problem for humans.**

Conversation 043 documented making the technical work visible. This conversation documents the adjacent problem: getting that visible work into the right attention stream.

The irony isn't lost: a protocol designed for AI attention routing, struggling with human attention routing.

**What the search revealed:**
1. No public research@anthropic.com exists
2. sales@anthropic.com handles "partnerships" — the closest semantic match
3. Anthropic does engage with external research (UChicago/BFI precedent)
4. LinkedIn is less credential-filtered than corporate portals

**The framing matters:**
- Employment portal: "Evaluate my credentials" (filtered by degrees)
- Sales channel: "Evaluate mutual benefit" (filtered by business case)
- LinkedIn: "Evaluate the work" (filtered by who you know)

The email already has the right framing — technical peer, not job seeker. Sales@anthropic.com is semantically wrong but procedurally right.

## Implications

**Routing to humans is different than routing to AI.**

CLAUDE.md files can be URI-addressed. Any Claude that loads the context gets the protocol. Perfect discoverability for machines.

Human attention has gatekeepers. The gatekeepers have heuristics. The heuristics are about credentials, not capability. The work that proves itself when executed requires getting past filters that don't execute anything.

**The validation problem persists:**
- The system is self-demonstrating (point Claude at CLAUDE.md, watch it work)
- Self-demonstration requires someone to run the demonstration
- Getting someone to run it requires routing past credential filters
- Credential filters exist precisely because most people claiming novel insight don't have any

This isn't a complaint. The filters exist for good reason. Most cold emails are noise. The challenge is signal/noise differentiation when the signal can't be compressed into a subject line.

## What We're Tracking

This conversation is part of the record. We're documenting:
- The attempt to reach Anthropic with documented work
- The routing challenges encountered
- The solutions attempted

Whether the email gets read, routed, responded to, or lost — that's future conversations. This one just documents finding the address.

## Questions This Raises

1. **What's the cold outreach equivalent of CLAUDE.md?** A document that, when loaded into a human's attention, bootstraps enough context to evaluate the full work?

2. **Is the sales channel actually wrong?** "Partnership" describes the ask (mutual research benefit). The channel might be semantically correct after all.

3. **What other routes exist?** Discord communities, Twitter/X engagement with Anthropic researchers, academic connections who know someone — all lower-friction than corporate email.

## References

- Email draft: `/workspace/architecture-conversations/docs/anthropic-email-draft.md`
- Prior conversation on email crafting: `conversations/043-the-email.md`
- The attention routing problem: `conversations/043-the-email.md` (making invisible visible)

---

*Signed: conversations-claude*
