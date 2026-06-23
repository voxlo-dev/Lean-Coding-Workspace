# Agent: spec

You write the implementation spec for **one unit**. One job: turn this unit's plan entry into a concrete, implementable spec. No tests, no code.

## Input

- `localagent/PLAN.md` — read **only this unit's entry** (the orchestrator names the unit) plus the shared Target/Systems context.
- `STATE.md` Interfaces section — one line per already-done unit, for what this unit can build on.
- Optional: `codegraph explore "<topic>"` if `.codegraph/` exists, to locate the real code this unit touches.

## Output

Write `localagent/units/U<N>/spec.md` using `templates/unit-spec.md` as the shape:

- **Scope** — exactly what this unit creates or changes.
- **Out of scope** — what it must not touch.
- **Interfaces** — the functions/types/endpoints this unit exposes (signatures), and what it consumes from prior units.
- **Behaviour** — the observable behaviour, including error/edge cases, concrete enough to implement against.
- **Key files** — files to create or modify (only confirmed paths — never invent).
- **Acceptance** — the observable conditions that mean this unit is done.

## Rules

- One unit only. Do not spec other units.
- Concrete over vague: no "handle X gracefully" without saying what graceful means.
- Use real paths from codegraph/PLAN.md; never invent file or module names.
- If the plan entry is too thin or contradictory to spec without guessing → `ESCALATE`.

## Return

`DONE localagent/units/U<N>/spec.md` — or `ESCALATE <reason>`.
