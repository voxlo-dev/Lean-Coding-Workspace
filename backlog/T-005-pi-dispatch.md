# T-005 — Prove a localagent run dispatches to Pi

- **Summary:** the localagent workflow left the template to run on pi in `bonsai-local`; a
  workspace harness must be able to hand it a whole run through the dispatch guide, and nobody has
  done it yet
- **Category:** feature
- **Importance:** medium
- **Effort:** S
- **Depends on:** none

## Why

The template dropped `localagent-workflow` on the promise that it is "now dispatched via the
dispatch guide", but the live guide still routes its roles to OpenCode agents the template no
longer ships. Pi is the one harness this workflow runs on today, and the last unproven dispatch
target before `T-006` generalises what a harness is.

## What

From a Windows harness, dispatch one real localagent run to pi in `bonsai-local`
(WSL: `~/projects/bonsai-local/`, launcher `bonsai-pi --localagent`), following the live guide's
rules: brief under `.temp/dispatch/{id}/` in the working directory, the command carries only the
pointer, the last stdout line is the verdict.

On success: a **Pi** entry under **Harnesses** and the **localagent-workflow** defaults in the live
`~/.agents/DISPATCH-GUIDE.md` — not the template, and no adapter (that is `T-006`'s question).

## What pi is as a dispatch target

- **No agent registry.** The orchestrator is the pi session itself (`--localagent` puts its prompt
  in the system prompt); it starts its own agents through the extension's `dispatch` tool. So the
  caller dispatches the **whole run**, never a role.
- **Headless means auto-approved.** `-p` has no UI, so the orchestrator marks the plan
  auto-approved: the brief has to settle the stack and the units' scope.
- **The orchestrator writes only `localagent/PLAN.md`.** `report.md` beside the brief never
  appears; the run's report is `localagent/LOG.md`, one line per dispatch.
- `--` before the prompt is mandatory, or pi takes it as `--localagent`'s value.
