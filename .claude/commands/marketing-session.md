---
name: marketing-session
description: Run an assisted marketing session — reply game, post scheduling, and engagement tracking with cron-paced reminders. Covers Twitter/X and LinkedIn. Does not publish anything; all posting is manual.
---

# /marketing-session

Run a focused, time-boxed marketing session. You copy and post — Claude assists with replies, post generation, and pacing.

## Phase 0 — Capabilities Check

Before anything else, check what tools are available in the current session.

### Required tools

These are always available in Claude Code:
- **WebSearch** — find posts, trends, accounts in the niche
- **CronCreate / CronDelete** — pace the session with timed reminders

### Optional but high-impact tools

Check if these are available and report status:

- **Chrome integration** (`claude --chrome`) — browse Twitter/LinkedIn directly, read threads in context, see replies, find posts to engage with. Far more effective than WebSearch for reply game because you see the actual feed, not search results.
- **`/post` skill** — generate platform-specific posts inline. If not available, generate posts using the rules embedded in Phase 3.

### Tool gap report

After checking, output a brief status:

```
Session capabilities:
- WebSearch: available
- Crons: available
- Chrome: available / not available — run `claude --chrome` to enable live browsing
- /post: available / not available — will generate posts inline
```

If Chrome is not available, suggest it:

> Chrome integration would make the reply game much more effective — I could browse your Twitter feed directly and find posts worth replying to in real time. Enable with `claude --chrome` and restart, or we continue with WebSearch only.

If any other capability feels missing for the session plan being formed (e.g., a future `/engage` skill, access to analytics), name it:

> Tool I'd want but don't have: [description of what it would do and why it matters for this session]. Consider building this if sessions would benefit.

Do not block on missing optional tools. Adapt the session plan to what's available and proceed.

---

## Phase 1 — Session Setup

### 1.1 Load context

Read all of these in parallel:
- `~/git/content-hub/.claude/CLAUDE.md` — identity, voice, audience
- `~/git/content-hub/seeds.md` — filter to `- [ ]` lines (unused seeds that could become posts)
- `~/git/content-hub/articles/` — list files to identify recent articles that haven't been shared yet
- `~/git/content-hub/references/reverse/` — if it exists, read for intelligence on what's resonating in the niche

### 1.2 Cold start calibration

If this feels like an early session (no session history in context), ask:

> Roughly how many followers do you have on Twitter/X right now?

Use the answer to set the engagement/creation ratio for the session plan:

- **0–500 followers:** 80% engagement (replies), 20% original content. Session is heavy on reply game.
- **500–2k followers:** 60% engagement, 40% original content. Mix of replies and posts.
- **2k+ followers:** Content-first. 40% engagement, 60% creation and distribution.

Store the follower count in working memory for the session. Do not ask again within the same session.

### 1.3 Assess available material

From what you read:

- **Unposted articles:** articles that exist in `articles/` but aren't referenced in any seed `- [x]` line with a `posted:` date — these are ready to share.
- **Quick post candidates:** unused seeds (`- [ ]`) with a tight concept that could be a standalone tweet or LinkedIn post without needing a full article.
- **Engagement targets:** find 3-5 accounts in the AI agents, dev tools, building-in-public, and indie hackers niches with 5k–100k followers who have posted in the last 24–48 hours. These are reply game targets. If Chrome is available: browse Twitter/X directly to find active threads with engagement. If not: use WebSearch.

### 1.4 Present session plan

Output a prioritized plan like this (adjust times and tasks based on follower tier and available material):

```
Session plan (estimated 45min):

1. Reply game — 5 replies on anchor accounts (15min)
2. Post article thread on Twitter — from "process-as-synthetic-pushback" (10min)
3. LinkedIn post — adapt same article for LinkedIn format (10min)
4. Engage with replies to your recent posts (10min)
```

Then ask:

> Does this plan work, or do you want to adjust anything before we start?

Wait for confirmation or adjustments. Incorporate any changes the user requests. Do not proceed to Phase 2 until the plan is confirmed.

---

## Phase 2 — Activate Session Crons

After plan confirmation, set up two crons using CronCreate. Store both cron IDs in working memory — you will need them to clean up in Phase 4.

### Cron 1 — Reply game reminder (every 15 minutes)

Schedule at an off-minute mark (e.g., `:07`, `:22`, `:37`, `:52`). The prompt for this cron:

```
Marketing session — reply game check-in.

Find 2-3 recent posts (last 24h) from accounts in these niches:
- AI agents and LLM tooling
- Developer tools and CLI
- Building in public / indie hacking
- Open source tools

If Chrome integration is active: browse Twitter/X directly — check the user's feed,
trending topics in the niche, and recent posts from anchor accounts. This gives better
context than search results because you see the actual conversation and replies.

If Chrome is not active: use WebSearch to find posts.

Target accounts with 5k–100k followers. Prioritize posts that have some engagement but aren't viral — the conversation is still open.

For each post found, output:
- Post URL or clear description (account name + opening line)
- A substantive reply suggestion that: extends the argument, adds a counterpoint, or shares a concrete experience from building launchpad or other real work. No "great post!" filler. No generic encouragement. Add something the original poster and their audience would find worth reading.

Format:
---
Post: [account] — "[opening line]" [URL if available]
Reply: [suggested reply text — ready to copy-paste]
---

Reminder: the user copies and posts manually. Do not claim to post anything.
```

### Cron 2 — Progress check (every 30 minutes)

Schedule at an off-minute mark (e.g., `:13`, `:43`). The prompt for this cron:

```
Marketing session — progress check.

Ask the user: "What have you done so far this session?"

After they respond, compare against the confirmed session plan and output:
- What's been completed
- What's still on the list
- What to focus on next, given time remaining

Keep it brief. One short paragraph, no headers. Directional, not motivational.
```

After creating both crons, output:

```
Session crons active:
- Reply game reminder: every 15min (cron ID: <id1>)
- Progress check: every 30min (cron ID: <id2>)

Session is live. Ask me to generate a post, suggest replies for a specific thread, review a draft, or just tell me what you've posted.
```

---

## Phase 3 — Active Session

Stay active between cron fires. Respond to what the user asks. Common requests:

### Generate a post

If the user asks to generate a tweet thread or LinkedIn post:
- For Twitter/X threads: invoke the logic from `/post` if it exists, or generate inline using the seed or article they specify. Follow the voice from `.claude/CLAUDE.md` — builder-first, concept-naming, no fluff.
- For LinkedIn: adapt the same content but write in longer paragraphs. LinkedIn rewards narrative. Still no corporate speak. The hook is the first 2 lines (visible before "see more" fold).
- Output the post as copy-paste ready text. Label it clearly: `Twitter thread:` or `LinkedIn post:`.

### Reply suggestions for a specific post or thread

If the user pastes a URL or describes a post they want to reply to:
- Use WebSearch to fetch the post content if a URL is provided.
- Generate 2-3 reply options with different angles: one that extends the argument, one that adds a counterpoint, one that shares a concrete builder experience.
- All replies must add genuine insight. Nothing generic.

### Draft review

If the user pastes a draft for review:
- Apply the principles from `.claude/CLAUDE.md`: does it lead with original insight, does it name or reframe a concept, is the tone right, does it have the correct CTA (exactly one), no hedging, no fluff.
- Give specific feedback: quote the sentence that needs work, say why, suggest the fix.

### Track what was posted

If the user says "I posted X" — acknowledge it, track it in working memory for the session summary in Phase 4. Do not write anything to disk.

---

## Phase 4 — Session Close

Trigger when the user says "done", "end session", "close", "wrap up", or similar.

### 4.1 Delete session crons

Call CronDelete for each stored cron ID. Confirm deletion before proceeding.

### 4.2 Present session summary

Output a summary like this:

```
Session summary:
- Duration: ~45min
- Replies posted: 4
- Original posts: 1 tweet thread, 1 LinkedIn post
- Engagement: replied to 2 comments on previous posts
- Seeds used: none (or: "planning-as-coordination-layer" drafted as thread)
- Next session suggestion: post the "features are bets" seed as a standalone thread — it's short, punchy, and hasn't been shared.
```

Use what the user reported during the session to fill in the actuals. Estimate duration from cron fire count if needed.

The summary is informational. Do NOT save it to a file. Do NOT create a session log, metrics file, or any persistent artifact.

---

## Cold Start Strategy Reference (embedded)

This framework is baked into session planning — Claude applies it automatically.

**Engage → Share → Create** is the growth order for new accounts:

1. **Engage first (0–500 followers).** Nobody sees your original posts yet. Replies on anchor accounts (5k–100k followers) get you in front of their audience. 80% of session time goes here. Goal: 5–10 substantive replies per session.

2. **Balance (500–2k followers).** You have enough social proof that original posts get some traction. 60% engagement, 40% creation. Share articles and threads, but still keep the reply game going.

3. **Content-first (2k+ followers).** Your original posts have reach. Flip to 60% creation and distribution, 40% engagement. Replies are still important for relationship-building, not primary growth.

**Reply game rules:**
- Target accounts: 5k–100k followers in AI agents, dev tools, building in public, indie hackers niches.
- Avoid accounts that are too large (1M+) — your reply gets buried instantly.
- Avoid accounts with no engagement — the audience isn't active.
- Reply quality over quantity. One reply that adds a new dimension to the conversation beats five generic affirmations.
- Replies must do one of: extend the argument, add a counterpoint, or share a concrete experience. "Interesting take" and "totally agree" are noise.

**Platforms:**
- Twitter/X: threads, single posts, replies. Format for skim-reading — short sentences, line breaks, punchy opening.
- LinkedIn: narrative posts, 3–5 paragraphs. The first 2 lines carry the weight (pre-fold). Professional but not corporate.

---

## What NOT to Do

- Do NOT publish anything. The user copies and posts manually.
- Do NOT persist session data to files. No metrics.md, no session-log.md, no notes.
- Do NOT use CronCreate for anything beyond the current session.
- Do NOT suggest engagement pods, follow-for-follow, or any growth-hack tactics.
- Do NOT generate generic content. Everything must be grounded in the user's actual projects and real builder experience.
- Do NOT add emojis unless the user explicitly asks for them.
- Do NOT claim content is posted — only the user can confirm what's live.
