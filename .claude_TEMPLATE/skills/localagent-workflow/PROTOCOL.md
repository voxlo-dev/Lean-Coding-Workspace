# localagent — Orchestration Protocol

Runner-neutral orchestration logic for the localagent workflow. This is the **single source of truth**; the Claude Code `SKILL.md` is only a thin wrapper that maps these steps onto the Agent tool. An external local-model runner can drive the same `agents/` prompts directly from this file.

The whole workflow is designed for a **weak (~30B) local model**: every agent gets a tiny, single-purpose context, and the orchestrator never holds artifact content — only the `STATE.md` ledger.

**What makes this workflow force TDD:** the tests and the production code are written by two different agents, separated by a **visibility wall** — the implementer never sees the test code. Both derive independently from a shared **contract** (interfaces/signatures) plus a **spec** (behaviour). Code that passes therefore satisfies the contract, not the test text.

## Roles

- **Orchestrator** — you. Pure control flow: plan, decompose, delegate, update `STATE.md`, enforce gates. Never write code, specs, contracts, or tests yourself. If you catch yourself doing content work, stop and dispatch the right agent.
- **Agents** (`agents/*.md`) — each does exactly one job from its declared inputs and writes one artifact (or one verdict). They never read beyond their declared inputs. The **implementer must never receive the test code** until the wall drops (see Rework).

## Artifacts

```
<repo>/localagent/
├── PLAN.md                 ← Planning phase (orchestrator)
├── STATE.md                ← you (the orchestrator) — re-read every round
└── units/U<N>/
    ├── spec.md             ← spec-architect (behaviour + acceptance)
    └── contract.md         ← spec-architect (interfaces + signatures)
```

Test code and production code go into the repo's normal source/test trees — not under `localagent/`. e2e writes a short report (`localagent/E2E.md`); docs are updated in place.

Everything under `localagent/` is a **run record**: committed with the run and frozen once the sprint closes — the docs step never edits it (see the project `AGENTS.md` doc map).

## STATE.md — your entire working memory

You do not rely on your context window to remember progress. After every step you **write** the new state to `STATE.md` and **re-read** it at the start of the next round. If your context is reset, `STATE.md` alone lets you continue.

```markdown
# State: <project>
Plan: localagent/PLAN.md
Phase: build            # planning | plan-gate | build | finalize | blocked

## Units
| ID | Title | Depends | Status | Attempts | Dir |
| --- | --- | --- | --- | --- | --- |
| U1 | … | — | done | 0 | units/U1 |
| U2 | … | U1 | impl | 2 | units/U2 |
| U3 | … | U1 | pending | 0 | units/U3 |

## Interfaces (1 line per done unit — cross-unit handoff)
- U1: exposes `foo(x): Bar` in src/foo.ts

## Blockers
- (none)
```

**Status ladder per unit:** `pending → specced → tests-red → impl → verified → done`.
**Attempts** counts red verifier cycles on the current unit — it drives the wall-drop and escalation thresholds.

## Phases

### Phase 1 — Planning

The orchestrator produces `localagent/PLAN.md` (target/systems, features, test strategy, and a **unit list** with dependencies). Keep units small — each one bounds every later agent's context.

- **Interactive (default, human present):** plan *with* the user — 2–3 tight rounds covering goal, must-haves vs nice-to-haves, constraints, what "done" looks like, and risky areas. Ground discovery in codegraph if the repo is indexed, otherwise a brief scoped look. Do not ask about test levels beyond what the test strategy needs.
- **Autonomous (headless runner, no human):** derive PLAN.md directly from the task brief using the same `templates/PLAN.md`.

Use `templates/PLAN.md`. The plan is deliberately small — it is the only artifact that describes the whole project.

### Plan gate (the one human gate)

Show the user the PLAN.md unit list and ask for explicit approval. **Stop until approved.** Silence is not approval. If they request changes, revise PLAN.md and re-show. This is the only routine pause — after it, the run is autonomous. (A headless runner treats the gate as a checkpoint: pause if a human is reachable, else record auto-approval in STATE.md.)

### Phase 2 — Build loop (per unit, just-in-time)

Seed `STATE.md` from the approved unit list (all units `pending`, `Attempts` 0). Then loop:

1. **Pick** the next actionable unit: dependencies all `done`, status ≠ `done`. Determine its next sub-step from the status ladder.
2. **Dispatch** the matching agent with its `agents/<name>.md` prompt + a 2–3 line brief + the path(s) to its declared input(s). Pass paths, never inline artifact content.

   | Sub-step (from → to) | Agent | Input | Output |
   | --- | --- | --- | --- |
   | pending → specced | `spec-architect` | this unit's PLAN entry + prior units' interface lines from STATE | `units/U<N>/spec.md` + `units/U<N>/contract.md` |
   | specced → tests-red | `test-author` | `spec.md` + `contract.md` | test files (verified failing) |
   | tests-red → impl | `implementer` | `spec.md` + `contract.md` **(never the tests)** | production code |
   | impl → verified | `verifier` | the unit's test files + the implicated src + prior `done` units' test paths (regression set) | verdict + behaviour-level failure report |

3. **Receive** the agent's 1-line status: `DONE <path>` | `RED <report-path>` | `ESCALATE <reason>` | `BLOCKED <reason>`.
4. **Update** `STATE.md`: advance the unit's status (or handle rework, below). When a unit reaches `done`, append one interface line for cross-unit handoff and reset its `Attempts` to 0. Re-read `STATE.md`, continue the loop.
5. When all units are `done`, go to finalize.

### The visibility wall & rework

The `test-author` writes tests from `spec.md` + `contract.md` and confirms they are **red** (fail because the code does not exist yet). The `implementer` then writes `src/` from `spec.md` + `contract.md` **only** — it is never handed the test code. The `verifier` runs the tests against the implementation.

On a `RED` verdict for unit U (this is red-cycle `k` = the unit's new `Attempts` value):

- **`k < 3` — wall stays up.** Re-dispatch `implementer` with the verifier's **behaviour-level** failure report (expected vs actual + the contract point / acceptance criterion that failed). Never forward the test source.
- **`k ≥ 3` — wall drops.** Re-dispatch `implementer` with the failure report **plus the unit's test file paths**, to break a hard deadlock.
- **`k ≥ 5` — stop.** `ESCALATE`: write the reason to STATE Blockers, set `Phase: blocked`, surface the exact blocker to the user.

**Verifier safety valve:** if the verifier judges the *test* to be wrong (it contradicts `contract.md`), it returns `ESCALATE test-mismatch` instead of `RED`. The orchestrator re-dispatches `test-author` (or `spec-architect` if the contract itself is wrong), not the implementer. This keeps a bad test from burning the implementer's attempt budget.

### Phase 3 — Finalize

1. **e2e** — dispatch `agents/e2e.md` **only if sensible**. Decide from bounded signals: a browser/UI surface (frontend/web/client dir, a UI-framework manifest, served HTML) or a meaningful integration surface (API, persistence, external service). If none apply, skip and note `e2e: no surface` in STATE.md. If e2e returns `FIXES_REQUIRED`, treat it like a blocker: record it, `Phase: blocked`, **stop and surface to the user** with the failing flow — do not auto-loop fixes (weak-model runs stop early rather than grind).
2. **docs** — dispatch `agents/docs.md` to update project docs from PLAN.md + what changed.
3. **memory** — persist durable decisions/gotchas and prune stale ones (in Claude Code: invoke the `maintain-memory` skill).
4. **commit / PR** per the project's version-control rules.

## Handoff contract (binds every agent)

- **Input:** a short brief + only the declared file path(s). Read nothing beyond the declared inputs. The implementer's inputs never include test files (until the wall drops on rework).
- **Output:** write your single artifact, then return one line — `DONE <path>` | `RED <report-path>` | `ESCALATE <reason>` | `BLOCKED <reason>`.
- **Boundaries:** never write outside your unit's scope. Never expand scope beyond your input.

## Escalation (weak-model guard)

- An agent that can't finish its step after ~5 distinct attempts returns `ESCALATE` rather than grinding.
- On any `ESCALATE` or `BLOCKED`: write the reason to `STATE.md` Blockers, set `Phase: blocked`, **stop the run**, and surface the exact blocker to the user. Do not re-dispatch around a blocker autonomously.

## Optional: codegraph

If the repo is indexed (`.codegraph/` exists), the `spec-architect` and `implementer` agents may use `codegraph explore "<topic>"` (CLI — works in any runner) to locate relevant code instead of broad file reads. Do not index the repo yourself.
