# localagent — Orchestration Protocol

Runner-neutral orchestration logic for the localagent workflow. This is the **single source of truth**; the Claude Code `SKILL.md` is only a thin wrapper that maps these steps onto the Agent tool. An external local-model runner can drive the same `agents/` prompts directly from this file.

The whole workflow is designed for a **weak (~30B) local model**: every agent gets a tiny, single-purpose context, and the orchestrator never holds artifact content — only the `STATE.md` ledger.

## Roles

- **Orchestrator** — you. Pure control flow: decompose, delegate, update `STATE.md`, enforce gates. Never write code, specs, or tests yourself. If you catch yourself doing content work, stop and dispatch the right agent.
- **Agents** (`agents/*.md`) — each does exactly one job from one or two input files and writes one artifact. They never read beyond their declared inputs.

## Artifacts

```
<repo>/localagent/
├── PLAN.md                 ← brainstorm agent
├── STATE.md                ← you (the orchestrator) — re-read every round
└── units/U<N>/
    ├── spec.md             ← spec agent
    └── testspec.md         ← testspec agent
```

Test code and production code go into the repo's normal source/test trees — not under `localagent/`. e2e and docs write short reports (`localagent/E2E.md`, doc updates in place).

## STATE.md — your entire working memory

You do not rely on your context window to remember progress. After every step you **write** the new state to `STATE.md` and **re-read** it at the start of the next round. If your context is reset, `STATE.md` alone lets you continue.

```markdown
# State: <project>
Plan: localagent/PLAN.md
Phase: build            # brainstorm | plan-gate | build | finalize | blocked

## Units
| ID | Title | Depends | Status | Dir |
| --- | --- | --- | --- | --- |
| U1 | … | — | done | units/U1 |
| U2 | … | U1 | tests-red | units/U2 |
| U3 | … | U1 | pending | units/U3 |

## Interfaces (1 line per done unit — cross-unit handoff)
- U1: exposes `foo(x): Bar` in src/foo.ts

## Blockers
- (none)
```

**Status ladder per unit:** `pending → spec → testspec → tests-red → impl-green → done`.

## Phases

### Phase 1 — Brainstorm (plan)

Dispatch `agents/brainstorm.md`. It produces `localagent/PLAN.md`: target/systems, features, test strategy, and a **unit list** (small work units with dependencies). The plan is deliberately rough and small — it is the only artifact that describes the whole project.

### Plan gate (the one human gate)

Show the user the PLAN.md unit list and ask for explicit approval. **Stop until approved.** Silence is not approval. If they request changes, re-dispatch brainstorm (or edit the plan) and re-show. This is the only routine pause — after it, the run is autonomous.

### Phase 2 — Build loop (per unit, just-in-time)

Seed `STATE.md` from the approved unit list (all units `pending`). Then loop:

1. **Pick** the next actionable unit: dependencies all `done`, status ≠ `done`. Determine its next sub-step from the status ladder.
2. **Dispatch** the matching agent with: its `agents/<name>.md` prompt + a 2–3 line brief + the path to its **one** input file.

   | Sub-step (from → to) | Agent | Input | Output |
   | --- | --- | --- | --- |
   | pending → spec | `spec` | PLAN.md (this unit's entry only) | `units/U<N>/spec.md` |
   | spec → testspec | `testspec` | unit entry + scan existing tests | `units/U<N>/testspec.md` |
   | testspec → tests-red | `test-implement` | `units/U<N>/testspec.md` | test files (verified failing) |
   | tests-red → impl-green | `implement` | `units/U<N>/spec.md` + the failing tests | production code (tests green) |

3. **Receive** the agent's 1-line status: `DONE <path>` | `ESCALATE <reason>` | `BLOCKED <reason>`.
4. **Update** `STATE.md`: advance the unit's status. When a unit reaches `done`, append one interface line for cross-unit handoff. Re-read `STATE.md`, continue the loop.
5. When all units are `done`, go to finalize.

### Phase 3 — Finalize

1. **e2e** — dispatch `agents/e2e.md` **only if sensible**. Decide from bounded signals: a browser/UI surface (frontend/web/client dir, a UI-framework manifest, served HTML) or a meaningful integration surface (API, persistence, external service). If none apply, skip and note `e2e: no surface` in STATE.md. If e2e returns `FIXES_REQUIRED`, treat it like a blocker: record it, `Phase: blocked`, **stop and surface to the user** with the failing flow — do not auto-loop fixes (weak-model runs stop early rather than grind).
2. **docs** — dispatch `agents/docs.md` to update project docs from PLAN.md + what changed.
3. **memory** — persist durable decisions/gotchas and prune stale ones (in Claude Code: invoke the `maintain-memory` skill).
4. **commit / PR** per the project's version-control rules.

## Handoff contract (binds every agent)

- **Input:** a short brief + exactly one or two file paths. Read nothing beyond the declared inputs.
- **Output:** write your single artifact, then return one line — `DONE <path>` | `ESCALATE <reason>` | `BLOCKED <reason>`.
- **Boundaries:** never write outside your unit's scope. Never expand scope beyond your input.

## Escalation (weak-model guard)

- An agent that can't finish its step after ~5 distinct attempts returns `ESCALATE` rather than grinding.
- On any `ESCALATE` or `BLOCKED`: write the reason to `STATE.md` Blockers, set `Phase: blocked`, **stop the run**, and surface the exact blocker to the user. Do not re-dispatch around a blocker autonomously.

## Optional: codegraph

If the repo is indexed (`.codegraph/` exists), the `spec` and `implement` agents may use `codegraph explore "<topic>"` (CLI — works in any runner) to locate relevant code instead of broad file reads. Do not index the repo yourself.
