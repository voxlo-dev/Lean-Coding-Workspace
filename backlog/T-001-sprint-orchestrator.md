# T-001 — Sprint orchestrator skill

- **Summary:** Sprint orchestrator skill — a delegation layer above the whole sprint that keeps the main thread at minimal context
- **Category:** feature
- **Importance:** medium
- **Effort:** L
- **Depends on:** none

## Why

Every workflow today runs *inside* the main conversation, so a sprint's worth of specs, package
work and doc passes accumulates in one context until compaction hits — usually mid-implementation,
where the loss costs the most. Newly confirmed subagent capabilities (resume-on-context, nested
agents) make a thin conductor above the whole sprint feasible: it holds only sprint slug, board
lines, artifact paths and open escalations, and delegates every phase.

Measured cost of delegation, from three trivial diagnostic subagents in this repo: **~30k tokens
of cold-start each** (32.7k / 33.0k / 35.2k for 1–3 tool calls apiece). Twenty delegated steps in
a sprint means ~600k tokens spent before any useful work. The skill therefore buys context quality,
**not** usage savings — anyone reaching for it to save budget is reaching for the wrong thing.

## What

A skill that conducts a full sprint end to end — `shape` → `open-sprint` → n × `dynamic-workflow`
→ `maintain-docs` → optional review/`release` → `close-sprint` — delegating each phase and holding
minimal state throughout. Success is a sprint completed without uncontrolled compaction of the
conductor's context.

Grounded in the capability probes run 2026-08-27:

| Capability | Status |
| --- | --- |
| Nested subagents (agent spawns agent) | Confirmed, three levels deep |
| Resume a completed agent on its own context | Confirmed, no loss — token, model and usage figures reproduced from transcript |
| Subagent asks the user directly | Ruled out — `AskUserQuestion`, `EnterPlanMode`, `ExitPlanMode` unreachable, directly and via `ToolSearch` |
| Subagent → main conversation mid-task via `SendMessage` | Open — schema lists `to: "main"` for background subagents; the probe was blocked by the auto-mode permission classifier before the tool could answer |

Shape the design must respect:

- **Interactive phases stay inline.** `shape` and `spec-design` are dialogues; with no user channel
  from inside an agent, delegating them means relaying every question through the conductor. Run
  them in the conductor's own context and hand off via file, accepting a controlled context reset
  at the phase boundary rather than a random one later.
- **Mechanical phases delegate.** `open-sprint`, packages, `maintain-docs`, `close-sprint`,
  `release` — clear inputs and outputs, no dialogue, immediate payoff.
- **Checkpoint after every phase** so compaction is survivable at any point. The conductor cannot
  see its own context fill and cannot invoke `/compact`; both are harness-owned, so self-managed
  handoff is impossible and proxy metrics are the only option.
- **`dynamic-workflow` becomes a sub-orchestrator**, now that nested agents are confirmed.
- **Question relay** uses the confirmed path: agent returns `NEEDS_DECISION` with the question and
  options → conductor asks the user → `SendMessage` resumes the agent on its intact context.

## Open questions

- Does `SendMessage` `to: "main"` work from a background subagent? Needs a retest under a relaxed
  permission mode. Not a blocker — the return/resume relay is confirmed and sufficient; the direct
  channel would only remove one round trip per question.
- Where exactly does the conductor hand off between the inline planning phases and the delegated
  build phases, given it cannot measure its own headroom?
