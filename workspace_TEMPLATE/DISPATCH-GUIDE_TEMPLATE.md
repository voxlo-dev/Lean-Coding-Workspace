# Dispatch Guide

<!--
CONTRACT — delete this block on the first real fill, leaving the one pointer line under the title.
Filled live by `dispatch-configurator` on the machine it describes; never from this template's
examples. Read by an orchestrator per run, so every line costs tokens in a run that dispatches:
keep a row only where a wrong guess would cost a failed dispatch, and every note to a clause that
changes a choice — measurements age, dates drift, and neither survives the next model swap.
`{...}` = replace.
Empty guide = no dispatch configured here. That is a valid state: say so and run inline.
-->

Machine-local dispatch configuration for {this PC}. Template:
`workspace_TEMPLATE/DISPATCH-GUIDE_TEMPLATE.md` in the workspace repo.

## Rules

- **A harness never dispatches itself** — where the target is the one you run in, call the agent
  natively. A second process buys nothing and pays another system prompt.
- **Dead connector → `BLOCKED`.** Take the fallback this guide names for that role, and if there is
  none, stop. Never fall back to your own model: that puts the strongest context in the run on
  unwalled, untracked work.
- **Only primary-capable agents** (`mode: all` / `mode: primary`). A subagent-only name is accepted,
  warned about, and silently replaced by the default agent — which runs your brief with no wall.
  An unexpected agent in the transcript header is a failed dispatch, not a result.
- **The command carries a pointer, not the payload.** The brief goes to
  `.temp/dispatch/{id}/brief.md` under the working directory and the command names that path — a
  quoted handoff dies at the shell's argument cap, and it belongs inside the working directory
  regardless, since path scoping is what stops the agent reading anywhere else, OS temp included.
- **The brief's shape belongs to the calling workflow**; these four parts are its floor, because the
  agent sees nothing else: absolute working directory · standing constraints from the user and the
  project · the task · paths to its inputs. Pass paths, never inlined content.
- **The last line of stdout is the verdict, nothing else** — a report, criteria met, concerns go to
  `report.md` beside the brief, the run's own output to `transcript.md` there too.
- **Sequential, one dispatch at a time.** A local endpoint serves one inference at a time; a free
  tier measures its queue.
- **≥64k context on any model listed below** — the target harness's own system prompt costs ~16k
  before your brief.

## Harnesses

### {harness — name only, no version}

- **Launch:** `{command with placeholders for agent, model, absolute dir, pointer}`
- **Providers:** {which of the entries below this harness resolves}
- **Agents:** {which agent set is registered here — a dispatch to a name it lacks is BLOCKED}
- **Notes:** {"verified on v{x}" — never the version in the heading, or it drifts at the next
  update · sandbox/network needs · live-view lever · traps that make a broken run look passing}

## Providers

| Provider | Type | Reached by | Notes |
| --- | --- | --- | --- |
| {unsloth} | local | {endpoint} | {load behaviour, hardware ceiling} |
| {openrouter} | aggregator | {auth} | {cost/limits; same model can cost more here than direct} |

## Models

Notes rank fitness for dispatch, not model quality; a paid key reorders every remote row.

| Name | Key | Provider | Notes |
| --- | --- | --- | --- |
| {Qwen 3.6 35B-A3B} | {provider/model-id} | {unsloth} | {one clause on when to pick it, or "untested" — a reader chooses from this, so relative speed and strength, never benchmark figures} |

## Workflow defaults

Role → what runs it. A role absent here has no dispatch: run it inline.

### dynamic-workflow

Optional per run — asked with the autonomy mode, not decided here.

| Role | Harness | Model | Fallback |
| --- | --- | --- | --- |
| package worker | {harness} | {model} | {model, else BLOCKED} |
| e2e agent | {harness} | {model} | {model, else BLOCKED} |
| other | {harness} | {model} | {model, else BLOCKED} |