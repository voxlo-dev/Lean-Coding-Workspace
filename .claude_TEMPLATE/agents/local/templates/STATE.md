# State: {Project Name}

Plan: localagent/PLAN.md
Phase: build            # planning | plan-gate | build | finalize | blocked

## Units

Status ladder: pending → specced → tests-red → impl → verified → done
Attempts = red verifier cycles on this unit (wall drops at 3, escalate at 5).

| ID | Title | Depends | Status | Attempts | Dir |
| --- | --- | --- | --- | --- | --- |
| U1 | {title} | — | pending | 0 | units/U1 |
| U2 | {title} | U1 | pending | 0 | units/U2 |

## Interfaces

One line per done unit — what later units can build on.

- {U1: exposes `foo(x): Bar` in src/foo.ts}

## Blockers

- (none)
