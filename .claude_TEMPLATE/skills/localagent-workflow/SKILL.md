---
name: localagent-workflow
description: "Use for a full feature build that must stay robust on a weak/local model: a sequential, context-frugal pipeline (plan gate → per-unit TDD loop → e2e/docs) where every step gets a tiny single-purpose context. Tests and code are written by separate agents behind a visibility wall so TDD is forced. Runner-neutral — drivable by any agent harness or an external local-model runner."
---

# Localagent Workflow

Sequential, context-frugal multi-agent build for a **weak (~30B) local model**. You are the
**orchestrator**: pure control flow — plan, decompose, delegate, update `STATE.md`, enforce gates.
Never write specs, contracts, tests or code yourself; catch yourself doing content work → stop and
dispatch the agent. Your context stays near-empty — `STATE.md` is your working memory, not your window.

**TDD is forced by construction:** `test-author` and `implementer` are separate agents behind a
**visibility wall** — the implementer never sees the test code. Both derive independently from a
shared `contract.md` (interfaces) + `spec.md` (behaviour), and the `verifier` runs the tests against
the code, so code that passes satisfies the contract, not the test text.

Nothing here assumes a particular harness or project layout: `agents/*.md` are plain prompts — one
job each, declared inputs only, one artifact, one status line back — and an external local-model
runner drives them straight from this file.

## Dispatch

Every agent runs in a **fresh, isolated context**; nothing carries between steps but the files. Its
prompt is the content of `agents/<name>.md` (read it) + a 2–3 line brief + the path(s) to its
declared inputs. **Pass paths, never inline artifact content.** Never pass test files to the
`implementer` until the wall drops.

- **One agent at a time, sequential** — a local model serves one inference at a time; keep that shape
  in every runner so a run behaves the same everywhere.
- **Where an agent runs** is the runner's choice: a local-model dispatch tool if the harness has one,
  a fresh chat against the local server, or the harness's own isolated-subagent mechanism. The
  prompts are written for a **~30B local model** — don't loosen them for a stronger one.
- **The wall holds only as far as the runner enforces it.** Best is file access scoped to each
  agent's declared inputs, so the implementer *cannot* reach the tests. Where the runner can only ask
  politely, the prompt rule is all there is — then never hand over a test path by accident.

## Artifacts

```
<repo>/localagent/
├── PLAN.md          ← planning, from templates/PLAN.md
├── STATE.md         ← the ledger, from templates/STATE.md
├── E2E.md           ← e2e report
└── units/U<N>/      ← spec.md (behaviour) + contract.md (interfaces), by spec-architect
```

Tests and production code go into the repo's normal trees, docs are updated in place. Everything
under `localagent/` is a record of one run: committed with it, never edited afterwards — it is not
living documentation.

**Write `STATE.md` after every step and re-read it at the start of the next round** — a context reset
must be survivable from it alone. Shape, status ladder and `Attempts` semantics: `templates/STATE.md`.

## Phase 1 — Plan, then the gate

Produce `localagent/PLAN.md` from `templates/PLAN.md`: target/systems, features, test strategy, and a
unit list with dependencies. Keep units small — each bounds every later agent's context. Interactive
by default: plan *with* the user in 2–3 tight rounds (goal, must-haves vs nice-to-haves, constraints,
what "done" looks like, risky areas), grounded in codegraph if indexed, else a brief scoped look.
Headless: derive PLAN.md from the task brief.

**Plan gate — the only routine pause.** Show the unit list, get explicit approval, **stop until
approved**; silence is not approval, requested changes → revise and re-show. After it the run is
autonomous. (Headless: pause if a human is reachable, else record auto-approval in STATE.)

## Phase 2 — Build loop

Seed `STATE.md` from the approved unit list, then loop:

1. **Pick** the next actionable unit — dependencies all `done`, status ≠ `done` — and read its next
   sub-step off the ladder.
2. **Dispatch:**

| Sub-step | Agent | Input | Output |
| --- | --- | --- | --- |
| pending → specced | `spec-architect` | the unit's PLAN entry + prior units' STATE interface lines | `units/U<N>/spec.md` + `contract.md` |
| specced → tests-red | `test-author` | `spec.md` + `contract.md` | test files, confirmed failing |
| tests-red → impl | `implementer` | `spec.md` + `contract.md` **(never the tests)** | production code |
| impl → verified | `verifier` | the unit's tests + implicated src + prior `done` units' test paths (regression set) | verdict + behaviour-level failure report |

3. **Update** `STATE.md` from the agent's status line (`DONE` · `RED` · `ESCALATE` · `BLOCKED`):
   advance the unit, or rework. On `done`, append its interface line and reset `Attempts`. Re-read,
   continue.
4. All units `done` → finalize.

**Rework on `RED`** (red-cycle `k` = the unit's new `Attempts`): re-dispatch `implementer` with the
verifier's **behaviour-level** report (expected vs actual + the contract point / acceptance criterion
that failed), never the test source. At `k ≥ 3` the **wall drops** — add the unit's test file paths to
break the deadlock. At `k ≥ 5`, escalate.

**Verifier safety valve:** a verifier judging the *test* wrong (it contradicts `contract.md`) returns
`ESCALATE test-mismatch` — re-dispatch `test-author`, or `spec-architect` if the contract is wrong,
not the implementer, so a bad test costs no attempt.

## Phase 3 — Finalize

1. **e2e** — dispatch `agents/e2e.md` only if a surface exists: browser/UI (a `frontend`/`web`/
   `client` dir, a UI-framework manifest, served HTML) or a meaningful integration one (API,
   persistence, external service). Neither → skip, note `e2e: no surface` in STATE.
2. **docs** — dispatch `agents/docs.md`.
3. **memory** — persist the run's durable decisions and gotchas wherever the project keeps them, and
   prune what went stale.
4. Update the project's work tracking if it has any, then commit / PR per its version-control rules.

## Escalation

An `ESCALATE`, a `BLOCKED`, `FIXES_REQUIRED` from e2e, or ~5 failed attempts on one unit: write the
reason to STATE Blockers, set `Phase: blocked`, **stop the run**, and surface the exact blocker to the
user. Never route around a blocker autonomously — a weak-model run stops early rather than grinds.

## Codegraph

Indexed repo (`.codegraph/` exists) → `spec-architect` and `implementer` may use
`codegraph explore "<topic>"` (CLI, so it works in any runner) instead of broad file reads. Do not
index the repo yourself.

## When NOT to use

The wall, the ledger and the tiny per-step contexts cost throughput and buy nothing where the model
can hold a whole feature at once — reach for this only when the model is weak or context discipline
is the priority, and never for a one-line fix.
