# T-001 — Sprint orchestrator skill

- **Summary:** Sprint orchestrator skill — a conductor above the whole sprint that dispatches every phase to another harness process and keeps its own context minimal
- **Category:** feature
- **Importance:** medium
- **Effort:** L
- **Depends on:** `T-011` for unattended runs — without it the conductor relays every question itself

## Why

Every workflow today runs *inside* the main conversation, so a sprint's worth of specs, package
work and doc passes accumulates in one context until compaction hits — usually mid-implementation,
where the loss costs the most. A thin conductor above the whole sprint holds only sprint slug,
board lines, artifact paths and open questions, and hands every phase to a separate process.

Dispatching whole runs out of process, rather than to in-harness subagents, also frees the choice
of harness and model per phase — the conductor can sit in the Claude app while `dynamic-workflow`
runs in OpenCode or Claude Code on whatever model `DISPATCH-GUIDE.md` names for it.

Measured cost of delegation, from three trivial diagnostic subagents in this repo: **~30k tokens
of cold-start each** (32.7k / 33.0k / 35.2k for 1–3 tool calls apiece); a dispatched harness adds
its own ~16k system prompt. The skill buys context quality, **not** usage savings.

## What

A skill that conducts a full sprint end to end — `shape` → `open-sprint` → n × `dynamic-workflow`
→ optional review/`release` → `close-sprint` — dispatching each phase through the dispatch guide
and holding minimal state throughout. Success: a sprint completed without uncontrolled compaction
of the conductor's context, and with the user reachable away from the terminal when `T-011` is
installed.

Shape the design must respect:

- **Dispatch rides the existing guide.** A workflow handed over whole is already a `whole run` row
  in `DISPATCH-GUIDE.md`; the conductor adds roles, not a second launch mechanism. Its rules hold —
  pointer not payload, last stdout line is the verdict, sequential per local endpoint.
- **Interactive phases follow the user channel.** `shape` and `spec-design` are dialogues. With
  `T-011` installed the dispatched run asks the user directly; without it they stay in the
  conductor's own context and hand off via file — a controlled reset at the phase boundary rather
  than a random one later.
- **Question relay without a channel:** the run ends with `NEEDS_DECISION` + question and options →
  the conductor asks the user → the harness's own session resume continues the run on its context
  (Claude Code resume, `opencode run --session`, Codex resume — per adapter manifest, unprobed).
- **Checkpoint after every phase** so compaction is survivable at any point. The conductor cannot
  see its own context fill and cannot invoke `/compact`, so proxy metrics are the only option.
- **Never wait blind** — the conductor wakes on the `dynamic-workflow` intervals while a run is out.

Capability probes, 2026-08-27, for the in-harness variant (Claude Code subagents):

| Capability | Status |
| --- | --- |
| Nested subagents (agent spawns agent) | Confirmed, three levels deep |
| Resume a completed agent on its own context | Confirmed, no loss |
| Subagent asks the user directly | Ruled out — `AskUserQuestion`, `EnterPlanMode`, `ExitPlanMode` unreachable |
| Subagent → main conversation via `SendMessage` `to: "main"` | Open — blocked by the auto-mode classifier before it could answer |

## Open questions

- Where does "the Claude app" as conductor actually run — Claude Code desktop, or Cowork? It
  decides which dispatch and wakeup levers exist, and whether a conductor can be a dispatch target
  of nothing (the guide's "a harness never dispatches itself").
- Handoff point between inline planning and dispatched building when no channel is installed,
  given the conductor cannot measure its own headroom.
