# Process as Synthetic Pushback

The guardrails conversation has it backwards. Everyone's asking how to catch agent mistakes after the fact — CI gates, human review, validation chains. But the reason agents produce bad output isn't that we're reviewing too little. It's that nobody's pushing back before they start.

In a human squad, pushback is organic. The dev says "this is underspecified." The designer says "this doesn't make sense for the user." The tech lead says "this is too big, split it." Agents don't do any of that. They execute. Cheerfully, confidently, and without a single objection. The process has to synthesize what was previously natural.

## The Sycophancy Problem Is a Design Problem

Claude will agree with your bad idea. Not always, but often enough that you can't rely on it to be the critical voice in the room. Ask it to implement a vague feature and it'll produce something — it won't stop and say "this spec has three unresolved design decisions and I refuse to start until you resolve them."

Most people treat this as a model behavior issue. Anthropic will fix sycophancy, eventually. But even if they do, the structural problem remains: an agent executing a task has no incentive to question the task itself. It optimizes for completion, not for correctness of the premise.

I stopped waiting for the model to push back. I built the pushback into the process.

## What Synthetic Pushback Looks Like

I built a plugin called [launchpad](https://github.com/rmolines/launchpad) that orchestrates feature delivery in phases: discovery, planning, delivery, review, ship. Each phase is designed to create friction where a human collaborator would have.

The friction isn't bureaucratic overhead. It's targeted resistance at the exact points where agents go wrong.

### Discovery: The Co-Founder Who Questions

The `/discovery` phase acts as a co-founder — not a note-taker. When I describe a feature that's actually three features glued together, it doesn't nod along:

> "This looks like 3 separate features: auth, dashboard, and billing. Each one delivers value independently and has different risks. I suggest we create separate discoveries for each. Which one do you want to prioritize?"

The trigger is explicit: if the problem statement requires "and" to connect unrelated concerns, or if planning would need 9+ deliverables, discovery refuses to continue as a single feature.

Before generating the PRD, five binary checks must pass. Fail one, no PRD:

| Check | Fail example | Pass example |
|---|---|---|
| Problem is falsifiable | "Users are frustrated with onboarding" | "35% of signups abandon before completing profile setup" |
| Success criteria are concrete | "Onboarding should feel fast" | "User completes setup in ≤ 3 steps without leaving the page" |
| Out-of-scope is specific | "Mobile is out of scope" | "iOS/Android native apps are out of scope. Web must be responsive to 375px." |
| Design decisions resolved | "We could use OAuth or magic links — TBD" | "Authentication uses magic links only. OAuth is explicitly out of scope." |
| Technical context included | "Use the existing auth system" | "Auth handled by `src/middleware/auth.ts` (JWT, RS256)" |

The validation for out-of-scope is particularly sharp: "For each out-of-scope item, would an agent working on adjacent code know to stop when it encounters it?" If the boundary is ambiguous, it's not a boundary.

The skill also searches the draft for `TBD`, `to be decided`, `could`, `might`, `option A/B`. Each one must be resolved before the PRD is finalized. No open questions survive into planning.

### Planning: The Tech Lead Who Says "Too Big"

Planning re-validates scope — if the PRD covers multiple independent features, it pushes back again, even though discovery already checked. Redundant friction is intentional.

Every deliverable must pass the verifiable slice test: "After this deliverable, can something be tested?" If not, it's too abstract. "Set up the data model" fails. "Create the User table and verify `npm run migrate` succeeds" passes.

Each deliverable prompt includes a `What NOT to do` field — explicit boundaries that prevent scope creep mid-execution. The agent can't argue "but it was easy to add" because the constraint was declared before it started.

### Review: The PM Who Holds the Line

Review doesn't evaluate its own output. It spawns an independent evaluator that classifies every change against the PRD:

- **ALIGNED** — directly implements what the PRD describes
- **DRIFT** — related but different from the original problem
- **EXTRA** — not mentioned in the PRD
- **MISSING** — required by the PRD but not found
- **OUT_OF_SCOPE_VIOLATION** — implements something explicitly excluded

Three outcomes, no middle ground: back to planning, back to delivery, or approved for ship. No "approved with reservations."

The rule on extras is the one that creates the most friction: extras are suspicious, not welcome. If the diff contains significant work not in the PRD, the review questions why. An agent that adds a caching layer because it seemed like a good idea gets flagged, not praised.

Out-of-scope violations are a hard line. Any violation sends it back to planning. No exceptions, no "but it was easy to add." The PRD excluded it for a reason.

## The Squad Mapping

Each phase replaces a specific human role's natural pushback:

| Human role | Natural friction | Synthetic replacement |
|---|---|---|
| Designer | "This doesn't make sense for the user" | `/discovery` rejects vague scope |
| Tech lead | "This is too big, split it" | `/planning` scope check + verifiable slices |
| QA | "You forgot this edge case" | `/review` validates against success criteria |
| PM | "That's not what we agreed" | `/review` blocks drift and out-of-scope |

## Why Upstream Friction Beats Downstream Gates

The entire guardrails industry focuses on catching problems after code is written: CI checks, linters, security scanners, human review. Those matter. But they're expensive — you've already spent the compute, the context, the time.

Synthetic pushback inverts the cost curve. A PRD that eliminates ambiguity before the agent writes a single line prevents the class of errors that no amount of post-hoc review catches reliably: wrong feature, right implementation. The agent built exactly what you asked for. The problem is that what you asked for was wrong.

When the spec is the pushback the agent won't give, the downstream gates become verification, not discovery. That's the difference between a safety net and a strategy.

## The Transferable Principle

You don't need my specific plugin to apply this. The principle is: identify where human collaborators would have pushed back, and encode that resistance as a hard gate in your process. Not a suggestion. Not a "consider whether." A gate that blocks forward progress until the condition is met.

Agents are going to keep getting better at execution. They're not going to start pushing back on your premises. That's not a bug to wait for a fix on — it's a structural property to design around.

---

I'm building launchpad as an open-source Claude Code plugin. If you're designing agent workflows and want to see how synthetic pushback works in practice, [check the repo](https://github.com/rmolines/launchpad).
