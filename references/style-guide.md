# Content Style Guide

## Voice Principles

**Builder-first.** Every claim comes from building, not from theory. "I built X and here's what happened" is the only credible position. Insight is earned by shipping, not by credentials.

**Direct.** No preamble. No "in this article I will explore." The first sentence is the sharpest sentence. Say the thing, then explain it.

**Technical but accessible.** Assume the reader can read code but don't require it. Code snippets are proof, not gatekeeping. A non-technical reader should still follow the argument; the code makes it verifiable for those who want to check.

**No corporate speak.** No "leverage synergies," no "best practices" without context, no "in today's rapidly evolving landscape." These phrases are noise filters — readers skip past them.

**Contrarian only when earned.** Challenge conventional wisdom only when you have direct evidence from building. Contrarianism without a concrete artifact to back it is just noise.

---

## Article Structure: The Concept-Naming Format

Every article gives a name to something that didn't have one, or reframes something that had the wrong name. If you can't name the concept in 4 words, the article isn't ready.

### Title
- Names the concept or reframes a known one
- 4-8 words, quotable standalone
- Should be a phrase someone would repeat in a conversation
- Examples:
  - "Planning Replaces the Git Coordination Layer"
  - "PRD Quality Gates Are Binary for a Reason"
  - "Verifiable Slices as the Unit of Decomposition"
  - "Process as Attention Protocol"
  - "The Verifiable Slice"

### Hook (First Paragraph)
- Challenges an assumption. State what most people believe, then immediately undercut it with what you actually found.
- No preamble. Start with the reframe, not the setup.
- Creates tension: the reader should feel a pull between the conventional view and your claim.
- Length: 2-4 sentences. Dense. No filler.

### Body
- Walk through the building experience: what problem surfaced, what you tried, what failed, what worked.
- Use headers to name sub-concepts. The headers themselves should be quotable.
- Include concrete artifacts: code blocks, file structures, example PRDs, prompt excerpts, CLI output.
- Specificity is credibility. "The planning phase took 3 iterations before the decomposition stabilized" is more credible than "planning can take multiple iterations."
- No listicles. Continuous prose with named sections. The argument should flow, not enumerate.

### Bridge
- Connect the specific insight to the broader principle it implies.
- One or two paragraphs. This is where you zoom out from "I built X" to "this suggests Y about how AI-assisted development works."
- The bridge is what makes the article worth sharing — it's the transferable insight, not just the personal story.

### CTA (Close)
Pick exactly one:
- **Follow for more** — if the article is a standalone insight with no direct tool tie-in
- **Try the tool** — if launchpad or another specific tool is directly relevant; link to repo
- **What's your experience** — if inviting discussion genuinely adds value (use sparingly)

Never all three. Never zero.

---

## Tone Markers

### DO

- Use "I built", "I found", "this broke", "what actually happened"
- Name sub-concepts with headers mid-article
- Show artifacts: actual file paths, actual prompts, actual outputs
- Write short sentences when the point is sharp
- Use code blocks to prove a claim, not just to illustrate
- Be specific about failure: what went wrong, at what step, with what consequence

### DO NOT

- "In this article we'll explore…"
- "Let me walk you through…"
- "Here are X things to know about…"
- Hedging: "might", "could potentially", "it seems like" — unless you're genuinely uncertain and the uncertainty is the point
- Passive voice as a hedge ("it was found that")
- Productivity-influencer energy: "this one insight changed how I work"
- Explaining what you're about to say before saying it

---

## Length Guidance

**Target range:** 800–1500 words for blog posts and Twitter long-form.

- Under 800: probably not fully developed. The bridge is likely missing or the body has no artifacts.
- Over 1500: probably has padding. Cut the parts that restate what was already said.

No padding to hit a word count. No artificial brevity to seem sharp. Length follows the concept.

---

## Example Hooks

These show the reframe style — challenge the assumption in the first breath.

---

**Example 1 — Planning as coordination layer:**

> Most teams use planning to communicate intent. I use it to eliminate communication entirely. When a plan is specific enough that an agent can execute it without asking clarifying questions, it has crossed a threshold most human-written plans never reach. That threshold turns out to be the actual leverage point.

---

**Example 2 — Decomposition as contract:**

> The conventional advice is to break work into small tasks. That's not wrong, but it misses the part that matters: each task has to be verifiable independently of the others. If you can't write a one-line test for "done," the task isn't small enough — it's just vague. There's a difference.

---

**Example 3 — The failure mode of flexibility:**

> I kept giving Claude latitude to "figure out the best approach." The output was fine but the variance was high — same prompt, different architectures, unpredictable. The fix wasn't more specific prompts. It was removing optionality from the plan itself. Constrained inputs produce consistent outputs. Obvious in retrospect; I had to ship three broken features to see it.

---

## Reference Voices

When in doubt, calibrate against:

- **Ashpreet Bedi** — concept-naming, precise language, takes that reframe how you think about something
- **Arvid Kahl** — practical founder experience, showing the work, systems thinking without abstraction for its own sake
- **Viv** — naming new patterns in AI before they have names

The through-line: all three name things. That's the move.
