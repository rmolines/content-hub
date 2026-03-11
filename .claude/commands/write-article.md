---
name: write-article
description: Generate a draft article from the seeds backlog. Picks an unused seed, researches current discourse, writes following the concept-naming style guide, and saves to articles/.
---

# /write-article

Generate a draft article from the content seeds backlog.

## Steps

### 1. Load seeds

Read `~/git/content-hub/seeds.md`. Filter to lines that start with `- [ ]` — these are unused seeds.

If no unused seeds exist, stop and output:

> No unused seeds. Run /capture to add ideas.

### 2. Pick a seed

Pick one unused seed at random. Do not default to the first one — distribute picks across the backlog. Show the selected seed clearly:

```
Selected seed: <full seed line>
```

Then ask:

> Write an article about this seed? (yes / pick another / specify which)

Wait for confirmation before continuing.

If the user says "pick another," repeat the random selection.
If the user says "specify which," list all unused seeds and let them choose by number.

### 3. Load context

Read all of these:
- `~/git/content-hub/references/style-guide.md` — writing format and tone rules
- `~/git/content-hub/.claude/CLAUDE.md` — strategic identity, audience, and principles
- The seed's context file at `~/git/content-hub/seeds/<slug>.md` (derive slug from the seed headline: lowercase, hyphens, no punctuation, max 6 words). This file contains the raw material and reasoning captured at the moment of insight — use it heavily to ground the article in specifics. If the file doesn't exist, proceed without it.

### 4. Research current discourse

Use WebSearch to find:
- What's currently being said about this topic on Twitter / X
- Recent blog posts or articles on the same theme
- Contrasting or opposing viewpoints
- Any named concepts or frameworks already in use

Search queries should be specific to the seed's angle, not generic. If the seed is about agent decomposition, search for that — not "AI productivity."

Synthesize what you find: what gap exists, what's been said, where the original angle from the seed creates differentiation.

### 5. Generate the article

Follow the concept-naming format from the style guide exactly:

**Title:** Names the concept in 4-8 words. Quotable standalone.

**Hook:** First paragraph challenges an assumption. No preamble. Direct reframe. 2-4 sentences.

**Body:** Walk through the building experience with concrete artifacts — code blocks, file structures, actual prompts, real outputs. Use headers to name sub-concepts. No listicles. No padding.

**Bridge:** One or two paragraphs connecting the specific insight to the broader principle.

**CTA:** Exactly one of: follow for more / try the tool / what's your experience.

Target length: 800–1500 words.

Apply DO / DO NOT rules from the style guide. If a sentence hedges, cut it or rewrite it as a direct claim. If a sentence explains what you're about to say, cut it.

### 6. Derive slug

Create a URL slug from the title:
- Lowercase
- Replace spaces with hyphens
- Remove punctuation
- Max 6 words

Example: "Planning Replaces the Git Coordination Layer" → `planning-replaces-git-coordination-layer`

### 7. Save the article

Save the full article to:

```
~/git/content-hub/articles/<slug>.md
```

The file should contain only the article content — no metadata headers, no frontmatter. Start directly with the title as an H1.

### 8. Update seeds.md

In `~/git/content-hub/seeds.md`, find the seed line that was used. Change `- [ ]` to `- [x]` and append ` | article: articles/<slug>.md` to the end of that line.

Example:
```
Before: - [ ] Planning eliminates coordination overhead | source: feature-lifecycle | captured: 2026-03-10
After:  - [x] Planning eliminates coordination overhead | source: feature-lifecycle | captured: 2026-03-10 | article: articles/planning-eliminates-coordination-overhead.md
```

### 9. Confirm output

Output:

> Article drafted: articles/<slug>.md — review and edit before publishing.

Do not auto-publish. The article is a draft. The human reviews and publishes manually.
