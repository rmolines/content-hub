# Handover

## marketing-session v2 — voice engine + interactive replies + compound learning — 2026-03-12

### What was done

Addressed three structural gaps in `/marketing-session` v1: sessions started from zero every time (no learning), replies were generated without knowing the user's stance, and posts had no voice profile so output was generic. Built three pillars to fix this.

**Pillar 1 — Voice Engine:** co-constructed voice profile at `marketing/voice-profile.md`. The file is seeded during the first session via a 5-question calibration, then evolves automatically. Both `/post` and `/marketing-session` read it before generating anything. `/post` now runs a brief learning loop on approval — it extracts what made the approved post work and appends it to the voice profile.

**Pillar 2 — Interactive Reply Flow:** Phase 3 of `/marketing-session` was refactored to ask for the user's stance before generating reply options (agree / disagree / take a specific angle). This eliminates the "here are 3 generic replies" problem. The reply is grounded in what the user actually thinks, not a neutral hedge.

**Pillar 3 — Compound Learning:** New Phase 5 at session close. Claude asks for what worked and what felt off, writes a timestamped journal entry to `marketing/journal/YYYY-MM-DD.md` using `_template.md` as the schema, updates the voice profile when a pattern has appeared 3+ times across entries, and appends an insight row to the journal log. Sessions accumulate rather than reset.

Pushed directly to main (no PR — this is a skills/content repo, not a product). Two commits: eae5776 (full v2) and the subsequent simplify pass.

### Key decisions

- **Voice profile as a living file, not a prompt injection:** The profile is a real artifact the user can read and edit. It grows from session data, not from the user filling out a form upfront. The first session runs calibration; subsequent sessions skip it if the file already exists.
- **Journal is a directory, not a flat log:** `marketing/journal/` with one file per session (`YYYY-MM-DD.md`) makes individual sessions reviewable. A flat `journal.md` would become unreadable after 20 sessions.
- **Learning threshold at 3 repetitions before promoting to voice profile:** Prevents noise from a single good session from hardening into permanent style. A pattern has to surface across multiple sessions before it becomes a rule.
- **Phase 5 is mandatory close, not optional:** The learning loop runs at every session end, not on a flag. This removes the decision cost that would cause it to get skipped.
- **`/post` participates in the learning loop:** Every approved post is a signal. `/post` extracts what worked and appends it to the voice profile immediately, so the profile improves even between sessions.

### Pitfalls discovered

- **Voice profile does not exist on first run:** If the user runs `/marketing-session` or `/post` before the first session that runs calibration, the voice profile read step silently finds nothing. Skills must handle the missing-file case gracefully and fall back to calibration or a sensible default. This is not currently protected.
- **Journal entries could drift from template:** `_template.md` is a reference schema, not enforced. If Phase 5 skips fields or adds new ones organically, the journal entries become inconsistent and harder to parse in future sessions. Consider making the template a strict schema that Phase 5 fills field by field.
- **`/post` learning loop on approval is unguarded:** If the user approves a post but the voice profile update step fails silently (file not found, write error), the learning is lost with no indication. No error handling currently in place.

### Key files

- `/Users/rmolines/git/content-hub/.claude/commands/marketing-session.md`: full v2 rewrite — Phase 3 interactive stance, Phase 5 compound learning loop, voice profile read at session start
- `/Users/rmolines/git/content-hub/.claude/commands/post.md`: voice profile integration (read before generating), learning loop on post approval (extracts what worked, appends to voice profile)
- `/Users/rmolines/git/content-hub/marketing/voice-profile.md`: living artifact — tone, style rules, patterns confirmed across sessions; seeded on first session, evolved by Phase 5 and `/post` approval loop
- `/Users/rmolines/git/content-hub/marketing/journal/_template.md`: per-session schema — date, what worked, what felt off, patterns, voice profile deltas

### Next steps

- Guard the missing voice profile case in both `/post` and `/marketing-session` — if file absent, run calibration inline rather than failing silently
- Build anchor accounts list (still outstanding from v1 next steps) — the interactive reply flow makes this more urgent because better post discovery = better stance options
- Mark seeds as posted on `/post` approval — still outstanding from v1, now even more important since `/post` already has an approval hook that could carry this
- Add a `/journal` skill for reviewing session history — surfaces patterns across entries, useful input before a session to recall what worked last time
- Consider enforcing template schema in Phase 5 by having Claude fill fields explicitly rather than free-writing the entry

---

## Content Hub — Initial delivery — 2026-03-11

### What was done

Built a "Claude Code CMS" repo to turn daily builder insights into published content. The system provides a `/capture` global skill for low-friction idea logging from any project CWD, a `/write-article` skill that picks a seed, researches current discourse, and drafts a concept-naming article. A first article was generated and committed, validating the end-to-end flow.

### Key decisions

- Seeds backlog is a flat markdown file (`seeds.md`) with checkbox status — no database, no scoring algorithm
- `/capture` is a global skill (`~/.claude/commands/capture.md`) so it works from any project CWD, hardcoding the hub path to `~/git/content-hub/seeds.md`
- Context files per seed live in `seeds/` directory (parallel to `seeds.md`) to enrich article generation
- Article format follows concept-naming style (title names a concept, hook challenges an assumption, body grounds in builder experience, CTA closes naturally)
- Manual publish for v1 — no auto-publishing to Twitter or any platform
- Language: English throughout for global AI/dev audience

### Pitfalls discovered

- The `seeds/` directory emerged organically (not in the PRD) as a way to store per-seed context files; `seeds.md` remains the source of truth for status tracking, but the two must stay in sync manually

### Key files

- `/Users/rmolines/git/content-hub/.claude/CLAUDE.md`: strategic brain — identity, target audience, content principles, article format; every session in this CWD inherits this context
- `/Users/rmolines/git/content-hub/seeds.md`: backlog with checkbox status, source project, capture date, and article path when used
- `/Users/rmolines/git/content-hub/seeds/`: per-seed context files enriching article generation
- `/Users/rmolines/git/content-hub/references/style-guide.md`: concept-naming format rules, tone markers, length guidance, example hooks
- `/Users/rmolines/git/content-hub/.claude/commands/write-article.md`: skill that selects a seed, researches discourse via WebSearch, drafts and saves an article, marks seed used
- `/Users/rmolines/.claude/commands/capture.md`: global skill — appends a formatted seed entry to `seeds.md` from any CWD
- `/Users/rmolines/git/content-hub/articles/`: generated drafts (4 seeds captured, 1 article drafted at delivery)

### Next steps

- Review and edit `articles/process-as-synthetic-pushback.md` — first draft ready for human polish and Twitter publish
- Run `/capture` regularly from feature-lifecycle and other project CWDs as insights surface
- Run `/write-article` to draft the remaining 3 unused seeds
- Decide on hosting platform for long-form articles (GitHub Pages, Substack, or gist) when Twitter thread format proves traction
- Consider a `/thread` skill that converts an article draft into a Twitter thread format

---

## head-of-marketing — /post + /marketing-session — 2026-03-12

### What was done

Added audience-building capability to content-hub. The problem was zero presence on Twitter and LinkedIn, no process for going from passive consumer to consistent producer, and no mental model of how audiences grow. Three blockers: inertia (every post was a decision from scratch), fear of posting to nobody, and no marketing knowledge.

Built two skills: `/post` transforms a seed, article, or free text into a platform-ready post (Twitter or LinkedIn), with a review loop until approved. `/marketing-session` runs an assisted 1-hour session — Claude evaluates what's available to post, produces a prioritized session plan, activates CronCreate-paced reminders (reply game every 15min, progress check every 30min), and closes cleanly when done. Updated `.claude/CLAUDE.md` with a Marketing section documenting the role, skills, cold start strategy, and platform tone differences.

PR #1, merged at af572e8.

### Key decisions

- **Session-assisted model over daily automation:** CronCreate is session-only and dies with the session — this is a feature, not a limitation. It forces dedicated blocks (1h, 2-3x/week) rather than ambient noise, and solves inertia without requiring persistent infrastructure.
- **Cold start strategy embedded in skills:** The Engage → Share → Create framework (Arvid Kahl) is baked into `/marketing-session` — the skill asks follower count at session start and adjusts the plan weighting (0-500: 80% reply game; 500-2k: 60/40; 2k+: content-first). Marketing knowledge is in the system, not in the user's head.
- **Chrome integration awareness added post-delivery:** After the skills were merged, it became clear that the reply game flow (WebSearch → find posts → suggest replies) would be much smoother with a way to open URLs directly. Noted as a tool gap but not blocked — user copies the URL manually for now.
- **Tool gap reporting:** `/marketing-session` explicitly surfaces the Chrome/browser gap in the reply game cron prompt so the user knows what's missing and why the workflow has friction there.

### Pitfalls discovered

- **Seed marking lifecycle gap:** Seeds used as the source for a `/post` are never marked as posted in `seeds.md`. The checkbox status stays `[ ]` even after a post goes live. There is no handoff between `/post` (which knows a seed was used) and the backlog status. Seeds can be picked up again in future sessions without knowing they already generated a published post.
- **Follower count asked every session:** `/marketing-session` asks for follower count at session start to calibrate the plan. This is intentional (no persistence by design — avoids a stale metrics file), but it creates mild friction every time. Accepted tradeoff for v1.

### Key files

- `/Users/rmolines/git/content-hub/.claude/commands/post.md`: skill — parses input, loads voice/reverse-intelligence context, generates platform-specific post (Twitter thread or LinkedIn text), runs adjustment loop until approved or discarded
- `/Users/rmolines/git/content-hub/.claude/commands/marketing-session.md`: skill — 4 phases (setup, cron activation, active session, close) with cold start strategy embedded; uses CronCreate for reply game and progress check
- `/Users/rmolines/git/content-hub/.claude/CLAUDE.md`: updated with `## Marketing` section — skills reference, cold start framework, platform tone (Twitter vs LinkedIn), intelligence source

### Next steps

- Build anchor accounts list (20-30 accounts, 5k-100k followers, AI agents / dev tools / building-in-public niches) — reply game cron needs this to find the right posts fast
- Mark seeds as posted on `/post` approval — add a step to `/post` that updates the checkbox in `seeds.md` when user confirms publish
- `/engage` standalone — suggest posts to comment on outside of a full session, for days with only 10min available
- Metrics tracking — decide on format (manual log in `references/metrics.md`?) once there's enough data to make the structure obvious; not before
