# content-hub

A Claude Code CMS for solo founders. Git repo as your content operations base — skills replace the dashboard.

## What it is

This repo is a lightweight content system built on Claude Code and markdown. No databases, no dashboards, no external dependencies. Insights are captured globally, organized locally, written and published from the command line.

The system is purpose-built for solo founders shipping AI products. Every article comes from real building — captured from the launchpad project at `~/git/feature-lifecycle/` and refined here.

## How it works

The flow: **capture** → **backlog** → **write** → **publish**.

1. Capture insights as you build — from any project CWD, use `/capture "insight"` to append to the global backlog.
2. Organize seeds in `seeds.md` — prioritize, tag, link to source repos.
3. Write articles in `articles/` — use `/write-article` from the content-hub CWD.
4. Publish — move finished articles to your blog or social feed.

## Usage

**Capture an insight** (from any repo):
```bash
/capture "Insight about your build"
```
Appends to the global backlog. Includes timestamp and CWD context.

**Write an article** (from content-hub):
```bash
/write-article
```
Creates a draft in `articles/` with strategic guidance from `.claude/CLAUDE.md`.

**Brainstorm or review strategy:**
```bash
# Open the repo and chat with Claude
```
Full context of identity, audience, and content strategy loaded from `.claude/CLAUDE.md`.

## Repo structure

```
content-hub/
├── README.md                  # This file
├── .claude/
│   ├── project.md            # Project spec (build, test, hot files)
│   └── CLAUDE.md             # Strategic brain (identity, voice, principles)
├── seeds.md                  # Ideas backlog
├── articles/                 # Draft and published articles
├── references/               # (future) Thesis, research, supporting docs
└── .gitignore
```

## Setup

Clone the repo and use it. Claude Code is required — ensure you have it installed:
```bash
git clone https://github.com/rmolines/content-hub.git ~/git/content-hub
cd ~/git/content-hub
```

No build steps, no dependencies, no setup beyond that. Open the repo and start capturing.

## Voice

Builder-first. "I built X, here's what I learned" — not theory or best practice. Content is grounded in shipping, not credentials. The target audience: AI practitioners building with Claude, solo founders moving fast, and open-source makers who care about primitives.
