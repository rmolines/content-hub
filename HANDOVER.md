# Handover

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
