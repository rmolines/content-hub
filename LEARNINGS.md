# Learnings

## 2026-03-10 — content-hub delivery

### /capture: input-polymorphic skills beat single-mode skills

**What happened:** The PRD spec'd `/capture` as accepting a text sentence. During delivery, it became obvious that real capture moments happen mid-work — reading a file, mid-conversation, or following a vague pointer. The delivered skill accepts four input forms: clean sentence, file path, conversation context, and vague reference.

**The generalizable pattern:** Any skill whose job is to extract an insight from work-in-progress should handle all three input modes from the start: (a) explicit text, (b) file path / artifact, (c) conversation context / no arg. Designing for only one mode produces a skill people won't actually use in the moments the insight surfaces.

**Second learning:** Saving a rich context file alongside each captured item (origin, raw material, "why this matters") is qualitatively different from saving the headline alone. The context file is what makes the item usable months later. Seeds without context files are just labels. Design capture tools to be context-first, not label-first.

**Where this applies:** Any future skill that captures, logs, or archives something from a work session: bug reports, decision records, research notes, etc.

---

## 2026-03-12 — head-of-marketing delivery

### Skills sharing mutable state need a write-back contract

**What happened:** `/post` and `/marketing-session` both operate on `seeds.md` — one reads it to surface material, the other executes posts from it. Neither writes back after use. After the delivery reviewed, the evaluator flagged that session planning will degrade over time because already-posted seeds keep surfacing. The PRD never specified a write-back step; both skills were designed independently against the same file.

**The generalizable pattern:** When two or more skills share a mutable state file (seeds, a queue, a log), at least one of them must own the write-back contract. "Independent skills that happen to read the same file" is not an architecture — it's a gap waiting to surface as UX degradation. Define the lifecycle explicitly in the PRD: which skill transitions an item from one state to the next, and what the transition looks like (field update, move to a different section, deletion).

**Second learning:** This gap is invisible at review time unless you trace the full lifecycle of a data artifact across all skills that touch it. A per-skill correctness check won't catch it. PRDs for multi-skill deliveries should include a state-transition diagram or at minimum an explicit list of every file each skill reads and writes.

**Where this applies:** Any future set of skills that share a queue, a log, or a capture file: task lists, idea backlogs, bug queues, session histories.

---

## 2026-03-12 — marketing-session v2 delivery

### Layered state with frequency-based promotion avoids premature rule solidification

**What happened:** v1 of `/marketing-session` had no mechanism to evolve its own behavior from session data. The naive fix would have been to ask the user to manually update the voice profile after each session. Instead, v2 introduced three state layers with explicit promotion rules: (1) per-session journal files in `marketing/journal/`, which capture raw patterns; (2) a voice profile in `marketing/voice-profile.md`, which holds durable rules; and (3) a frequency threshold — a pattern must appear 3+ times across journal entries before it gets promoted to the voice profile. The threshold prevents premature promotion of one-off observations into permanent rules.

**The generalizable pattern:** When a skill system needs to evolve its own behavior from usage data, don't write observations directly to the rule layer. Introduce an intermediate event log (journal, history, log file) and a promotion threshold. Observations go into the log cheaply and without commitment. Promotion to a rule requires evidence of recurrence. This gives the system a memory without locking in behavior from a single data point.

**Second learning:** A write-back that's conditional on user approval (as in `/post`'s diff analysis loop) is a different contract than a write-back that's automatic. Conditional write-back is appropriate when the rule being learned is about subjective preference — the user must confirm the pattern before it's generalized. Automatic write-back is appropriate when the pattern is objective (e.g., 3+ occurrences of the same result). Both belong in the same system; they apply to different kinds of knowledge.

**Where this applies:** Any skill that needs to personalize over time: writing assistants that learn voice preferences, code generation tools that learn style conventions, session-based workflows that accumulate engagement intelligence. The three-layer pattern (event log → threshold → rule layer) is reusable wherever you want a system that improves without human curation of every update.
