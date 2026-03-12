---
name: post
description: Transform a seed, article, or free text into a platform-specific social media post (Twitter or LinkedIn). Adapts voice and format per platform. Includes an adjustment loop until approved.
---

# /post

Generate a platform-specific social media post from a seed, article, or free text.

**Usage:** `/post <platform> [source]`

- `<platform>` — `twitter` or `linkedin` (required)
- `[source]` — optional: a seed headline, article slug, file path, or free text

## Steps

### 1. Parse input

Extract `<platform>` from the first argument. If it is not `twitter` or `linkedin`, stop and output:

> Platform must be `twitter` or `linkedin`.

Extract `[source]` from the remaining arguments. Treat it as one of:
- A file path (starts with `/` or `~/`, or ends in `.md`) — load that file directly
- An article slug (matches a file in `~/git/content-hub/articles/<slug>.md`) — load that article
- A seed headline (matches a line in `~/git/content-hub/seeds.md`) — use that seed
- Free text — use it as the raw concept to post about

If no source is provided, read `~/git/content-hub/seeds.md`, filter to lines starting with `- [ ]` (unused seeds), list them numbered, and ask:

> Which seed do you want to post about? (enter number or paste text)

Wait for the user to respond before continuing.

### 2. Load context

Always read:
- `~/git/content-hub/.claude/CLAUDE.md` — voice, tone, audience identity, and strategy principles
- `~/git/content-hub/marketing/voice-profile.md` — voice constraints: tone, banned words, AI anti-patterns, reference samples

If source is an article path or slug: read the full article file.

If source is a seed headline: derive the slug (lowercase, hyphens, no punctuation, max 6 words from the headline) and attempt to read `~/git/content-hub/seeds/<slug>.md`. If the file exists, use it heavily — it contains the raw reasoning and context captured at the moment of insight. If it does not exist, proceed without it.

If `~/git/content-hub/references/reverse/` exists: read the most recent 2-3 files in that directory (sort by modification time, descending). Use them as intelligence on what post formats and angles have worked in this niche. Do not copy — extract patterns.

### 3. Identify the core insight

Before writing, extract the one thing worth saying. State it internally as a single sentence:

> "The insight is: [X]"

If you cannot extract a single sharp insight from the source material, stop and ask:

> I can't find a clear insight to anchor the post. What's the one thing you want the reader to take away?

Do not write a post that is vague or summarizes instead of making a claim.

Check the insight and planned framing against the voice profile's banned words and AI anti-patterns. If the natural phrasing would violate a constraint, find an alternative before drafting.

### 4. Generate the post

Apply the correct format for the platform.

---

#### Twitter format rules

Apply all constraints from `marketing/voice-profile.md`: tone, banned words, AI anti-patterns. If reference samples exist in the profile, match their style and cadence.

Decide whether the insight fits in a single tweet (≤280 chars) or requires a thread (3-8 tweets).

**Single tweet:**
- State the insight directly. One claim, no hedging.
- If contrarian: name what most people believe, then cut it.
- No links in the tweet body — if a link is relevant, suggest it as a reply.
- 0-2 hashtags maximum. Only include if they surface to a real audience.

**Thread:**
- Tweet 1 is the hook: must create curiosity, state a surprising claim, or name a problem the reader recognizes. It must work as a standalone tweet.
- Each subsequent tweet carries a single sub-point. Not a continuation of a sentence — a complete thought.
- Last tweet: either a direct takeaway or a single-sentence CTA (follow / try the tool / reply with experience). Never all three.
- Number each tweet `[1/N]` where N is the total count.

**Tone for both:** informal, first person, builder-first. "I built X" not "X was built." No corporate language. No productivity-influencer phrasing ("game-changer", "unlock", "5 tips").

Do not add emojis unless the user explicitly asks.

---

#### LinkedIn format rules

Apply all constraints from `marketing/voice-profile.md`: tone, banned words, AI anti-patterns. If reference samples exist in the profile, match their style and cadence.

Write a text post of 1000-1500 characters.

**Structure:**
- Line 1-2 (hook): must create tension or name a surprising insight. This is what shows before the "see more" fold. Treat it as the headline. Make the reader need to click.
- Body: walk through the insight with one concrete example or artifact. Use short paragraphs and line breaks — walls of text get skipped.
- Lead with "so what" — interpret the technical insight for a broader audience, not just developers.
- End with one question or explicit invitation to comment. This drives algorithm reach.
- Suggest 3-5 relevant hashtags at the end (on their own line).

**Tone:** professional but authentic. "I learned X" not "studies show X." First person. No hedging. No listicle structures ("Here are 5 reasons...").

**If the source is technical:** after the post, suggest a carousel outline as an alternative format — 5-7 slide titles only, no content. Label it clearly as optional.

Do not add emojis unless the user explicitly asks.

---

### 4b. Humanizer pass

Before presenting, run the generated post through the humanizer patterns (`~/.claude/skills/humanizer/SKILL.md`):

1. Scan for AI patterns: em-dashes, negative parallelisms ("not just X, it's Y"), significance inflation, AI vocabulary, copula avoidance, rule-of-three, superficial -ing phrases
2. Rewrite any problematic sections
3. Self-audit: "What makes this obviously AI generated?" Fix remaining tells.
4. Present only the final humanized version

For single tweets (short text), a quick mental pass is enough. For threads and LinkedIn posts, do the full two-pass process.

### 5. Present the output

Format the output clearly.

**For a Twitter single tweet:**
```
— Tweet —
<tweet text>

Char count: <N>/280
```

If a link is relevant, add:
```
— Suggested reply —
<link + one sentence context>
```

**For a Twitter thread:**
```
— Thread —

[1/N] <tweet text> (<char count>/280)

[2/N] <tweet text> (<char count>/280)

...
```

**For LinkedIn:**
```
— LinkedIn post —

<hook line 1>
<hook line 2>

[see more fold]

<body>

<closing question>

<hashtags>

Char count: <N>
```

If carousel outline was generated, show it after the post under `— Optional: carousel outline —`.

After presenting, ask:

> Publish this? (yes / adjust [what] / try different angle)

Do not auto-publish. Do not interact with any API.

### 6. Adjustment loop

If the user says "adjust [what]": apply the requested change, re-present the full post with the same formatting, and ask the same question again.

If the user says "try different angle": generate a new post from a different entry point — different hook, different framing of the same insight — and present it.

If the user says "yes": copy the approved post to the user's clipboard using `pbcopy` (run via Bash: `printf "%s" "<post text>" | pbcopy`). Output:

> Copied to clipboard. Paste and publish.

After the user approves, analyze the diff between the first generated version and the final approved version. If the user made meaningful edits (not just typo fixes):
- Identify what changed and why (tone shift, word choice, structure)
- Suggest updating the voice profile: "I noticed you [changed X to Y]. Should I update the voice profile to reflect this preference?"
- If yes: append the learned pattern to the appropriate section in `marketing/voice-profile.md`
- If the post is strong, suggest adding it as a reference sample: "Add this to voice profile reference samples? (yes/no)"

If the user discards the post: stop without saving anything.

Do not save post drafts to disk unless the user explicitly asks.

---

## What NOT to do

- Do not auto-publish or call any social media API.
- Do not generate generic motivational content. Every post must anchor to a specific insight or artifact from the source material.
- Do not use productivity-influencer language: "5 tips", "game-changer", "unlock your potential", "the secret to", "you need to know this."
- Do not add emojis unless the user explicitly asks.
- Do not create carousel PDFs or images — only suggest carousel slide titles as plain text.
- Do not pad a thin insight into a long post. If the source material is weak, say so.
