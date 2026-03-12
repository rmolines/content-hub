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
- `~/git/content-hub/marketing/voice-profile.md` — voice profile (tone, banned words, AI anti-patterns, reference samples)
- `~/git/content-hub/marketing/journal/` — read the last 3-5 session files (sorted by date descending). Use previous session patterns, engagement data, and anchor accounts to inform session planning. Avoid repeating approaches that didn't work. Prioritize angles and accounts that showed strong engagement.

### 1.2 Journal-informed planning

If journal entries exist from previous sessions, extract:
- **What worked:** angles and post types that got engagement
- **What didn't:** approaches to avoid or deprioritize
- **Anchor accounts:** accounts that responded well — prioritize engaging with them again
- **Patterns trending toward promotion:** any pattern close to the 3-occurrence threshold

Use this intelligence throughout the session. Reference it when suggesting angles for replies.

### 1.3 Cold start calibration

If this feels like an early session (no session history in context), ask:

> Roughly how many followers do you have on Twitter/X right now?

Use the answer to set the engagement/creation ratio for the session plan:

- **0–500 followers:** 80% engagement (replies), 20% original content. Session is heavy on reply game.
- **500–2k followers:** 60% engagement, 40% original content. Mix of replies and posts.
- **2k+ followers:** Content-first. 40% engagement, 60% creation and distribution.

Store the follower count in working memory for the session. Do not ask again within the same session.

### 1.4 Assess available material

From what you read:

- **Unposted articles:** articles that exist in `articles/` but aren't referenced in any seed `- [x]` line with a `posted:` date — these are ready to share.
- **Quick post candidates:** unused seeds (`- [ ]`) with a tight concept that could be a standalone tweet or LinkedIn post without needing a full article.
- **Engagement targets:** If Chrome is available: browse the user's Twitter/X **For You feed** directly. This surfaces the most relevant posts algorithmically. Pick 3-5 posts worth replying to. If Chrome is not available: use WebSearch to find 3-5 accounts in the AI agents, dev tools, building-in-public, and indie hackers niches with 5k-100k followers who have posted in the last 24-48 hours. If journal has anchor accounts that responded well previously, prioritize checking their recent posts.

### 1.5 Present session plan

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

Primary method (Chrome required): Open the user's Twitter/X For You feed. Scroll through
and find 2-3 posts worth replying to. The For You feed surfaces posts the algorithm
considers relevant — this is where natural engagement happens.

Fallback (no Chrome): Use WebSearch to find recent posts from accounts in these niches:
- AI agents and LLM tooling
- Developer tools and CLI
- Building in public / indie hacking
- Open source tools

Target accounts with 5k–100k followers. Prioritize posts with some engagement but not
viral — the conversation is still open.

For each post found, show the FULL original post text and URL — do NOT summarize or
paraphrase. Then suggest angles. Do NOT generate full replies.

Format:
---
@account (Xk followers) — [URL]
"[FULL post text, quoted exactly]"

Angle options: agree+extend / counterpoint / share experience from [relevant project]
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
- Read `marketing/voice-profile.md` and apply all constraints: tone, banned words, AI anti-patterns. If the voice profile has reference samples, match their style.
- **Humanizer pass:** after generating the draft, run it through the humanizer skill (`~/.claude/skills/humanizer/SKILL.md`). Apply the two-pass process: rewrite to remove AI patterns, then audit ("what makes this obviously AI generated?") and revise. Present only the final version to the user.
- Output the post as copy-paste ready text. Label it clearly: `Twitter thread:` or `LinkedIn post:`.

### Reply to a specific post or thread

If the user pastes a URL or describes a post they want to reply to:

1. **Present the post** — use Chrome (preferred) or WebSearch to fetch the post content. Show:
   - Author, handle, and follower count (if visible)
   - **Full original post text** — quote it exactly, do not summarize or paraphrase
   - Direct URL to the post
   - Key replies already posted (if visible — avoid repeating what's been said)

2. **Ask stance** — before generating anything, ask:
   > Do you agree, disagree, or want a different angle on this?

   Wait for the user to respond. Do NOT generate a reply until you have their direction.

3. **Generate one draft** — based on the user's stance:
   - "agree" → generate an agree+extend reply: acknowledge the point briefly, then add a new dimension from builder experience
   - "disagree" → generate a respectful counterpoint grounded in concrete experience
   - specific angle provided → follow that direction
   - Apply voice profile constraints. Never use banned words. Match the tone defined in the profile. The reply must add genuine insight — no filler.
   - **Humanizer pass:** run the draft through the humanizer patterns (`~/.claude/skills/humanizer/SKILL.md`) before presenting. Remove em-dashes, negative parallelisms, AI vocabulary, and significance inflation. For short replies (1-2 sentences), a quick mental pass is enough — no need for full two-pass process.

4. **Fine-tuning loop** — present the draft and ask:
   > Use this? (yes / adjust [what])

   Maximum 2 refinement rounds. If not converged after 2, ask:
   > What's missing? Tell me the core point you want to make and I'll rewrite from scratch.

5. **Final copy** — copy the approved reply to the user's clipboard using `pbcopy` (run via Bash: `echo "<reply text>" | pbcopy`). Confirm:
   > Copied to clipboard. Paste and post.

### Draft review

If the user pastes a draft for review:
- Apply the principles from `.claude/CLAUDE.md`: does it lead with original insight, does it name or reframe a concept, is the tone right, does it have the correct CTA (exactly one), no hedging, no fluff.
- Read `marketing/voice-profile.md` and check the draft against all constraints: banned words, AI anti-patterns, tone match. Flag any violations specifically.
- Give specific feedback: quote the sentence that needs work, say why, suggest the fix.

### Track what was posted

If the user says "I posted X" — acknowledge it, track it in working memory for the session summary in Phase 4. Do not write anything to disk here.

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

The summary is informational. Do NOT save it to a file here — that happens in Phase 5.

---

## Phase 5 — Compound Learning

Runs automatically after Phase 4 close — no opt-in needed.

### 5.1 Pattern capture

For each reply and post generated in this session, ask the user (briefly, rapid-fire):

> Quick debrief — for each item, tell me: posted? any engagement? (just rough: nothing / some likes / good conversation / took off)

List the items from the session for easy reference. Accept terse answers.

Then internally classify each item:
- Angle used: agree+extend / counterpoint / reframe / concrete experience / original insight
- Post type it replied to: hot take / tutorial / build log / question / announcement
- Result: no engagement / some / strong

### 5.2 Journal update

Write today's session to `~/git/content-hub/marketing/journal/YYYY-MM-DD.md` (one file per day). If a file for today already exists, append the new session entry. Use this format:

```
## Session — YYYY-MM-DD

**Duration:** ~Xmin
**Replies posted:** N
**Original posts:** description

### Highlights
- [what worked, what didn't — based on user's debrief]

### Anchor accounts engaged
- @account — topic — result

### Patterns observed
- [angle] on [post type] → [result]

### Next session suggestion
- [what to prioritize based on patterns + unused seeds]
```

### 5.3 System evolution

After writing the journal entry, read the last 5-10 journal files from `marketing/journal/`. If any pattern appears 3+ times with positive results:

- Promote it to a rule. Output:
  > Pattern detected: "[pattern description]" has worked [N] times. Promoting to voice profile / skill instruction.

- Write the rule to the appropriate place:
  - Voice/tone pattern → append to `marketing/voice-profile.md` in the relevant section
  - Engagement pattern → add as instruction in this skill's Phase 3

If no pattern has hit 3 occurrences yet, say:
> No patterns ready for promotion yet. [N] sessions until next review.

### 5.4 Next session suggestion

Based on the journal patterns + available seeds in `seeds.md`, suggest what the next session should focus on. One sentence.

Output:
> Session complete. Journal updated. Next session: [suggestion].

---

## What NOT to Do

- Do NOT publish anything. The user copies and posts manually.
- Do NOT persist session data to files other than `marketing/journal/YYYY-MM-DD.md`.
- Do NOT use CronCreate for anything beyond the current session.
- Do NOT suggest engagement pods, follow-for-follow, or any growth-hack tactics.
- Do NOT generate generic content. Everything must be grounded in the user's actual projects and real builder experience.
- Do NOT add emojis unless the user explicitly asks for them.
- Do NOT claim content is posted — only the user can confirm what's live.
