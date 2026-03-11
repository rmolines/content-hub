# Learnings

## 2026-03-10 — content-hub delivery

### /capture: input-polymorphic skills beat single-mode skills

**What happened:** The PRD spec'd `/capture` as accepting a text sentence. During delivery, it became obvious that real capture moments happen mid-work — reading a file, mid-conversation, or following a vague pointer. The delivered skill accepts four input forms: clean sentence, file path, conversation context, and vague reference.

**The generalizable pattern:** Any skill whose job is to extract an insight from work-in-progress should handle all three input modes from the start: (a) explicit text, (b) file path / artifact, (c) conversation context / no arg. Designing for only one mode produces a skill people won't actually use in the moments the insight surfaces.

**Second learning:** Saving a rich context file alongside each captured item (origin, raw material, "why this matters") is qualitatively different from saving the headline alone. The context file is what makes the item usable months later. Seeds without context files are just labels. Design capture tools to be context-first, not label-first.

**Where this applies:** Any future skill that captures, logs, or archives something from a work session: bug reports, decision records, research notes, etc.
