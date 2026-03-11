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
