# T-001 — Sprint orchestrator beyond Claude Code

- **Summary:** prove `sprint-orchestrator` end to end and carry it past Claude Code → Claude Code — other harnesses as conductor or run target, unattended via the user channel
- **Category:** feature
- **Importance:** medium
- **Effort:** M
- **Depends on:** `T-011` for unattended runs

## Why

`workspace_TEMPLATE/skills/sprint-orchestrator/` exists, written for one route: a Claude Code
desktop conductor dispatching headless `claude -p` runs, escalations relayed through the
conductor's chat. That route is designed from `--help`, not yet run, and every other harness lacks
a probed headless-run row in its manifest.

Measured cost of delegation, from three trivial diagnostic subagents in this repo: **~30k tokens
of cold-start each** (32.7k / 33.0k / 35.2k for 1–3 tool calls apiece); a dispatched harness adds
its own ~16k system prompt. The skill buys context quality, **not** usage savings.

## What

- **Prove the Claude route:** one real sprint through Plan and Conduct — a `NEEDS_DECISION` resumed
  on its session, a `review`-mode spec stop, a fresh-chat resume from the Pipeline section. Then
  `/dispatch-configurator` writes the live guide's `sprint-orchestrator` section from a passing probe.
- **Other harnesses:** probe the manifests' **Headless run** row (OpenCode `run --session`, Codex
  resume) and add their routes.
- **Unattended:** with `T-011` installed, runs ask the user directly instead of through the
  conductor.

Capability probes, 2026-08-27, for the in-harness variant (Claude Code subagents):

| Capability | Status |
| --- | --- |
| Nested subagents (agent spawns agent) | Confirmed, three levels deep |
| Resume a completed agent on its own context | Confirmed, no loss |
| Subagent asks the user directly | Ruled out — `AskUserQuestion`, `EnterPlanMode`, `ExitPlanMode` unreachable |
| Subagent → main conversation via `SendMessage` `to: "main"` | Open — blocked by the auto-mode classifier before it could answer |

## Open questions

- Whether a headless run in `--permission-mode auto` gets blocked by the classifier on routine
  workflow steps (commits, test runs) — the first proving run answers it.
