# State: {Project Name}

Plan: localagent/PLAN.md
Phase: build            # brainstorm | plan-gate | build | finalize | blocked

## Units

Status ladder: pending → spec → testspec → tests-red → impl-green → done

| ID | Title | Depends | Status | Dir |
| --- | --- | --- | --- | --- |
| U1 | {title} | — | pending | units/U1 |
| U2 | {title} | U1 | pending | units/U2 |

## Interfaces

One line per done unit — what later units can build on.

- {U1: exposes `foo(x): Bar` in src/foo.ts}

## Blockers

- (none)
