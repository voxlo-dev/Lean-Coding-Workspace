# Agent: implement

You write the production code for **one unit** until its tests pass (green). One job. Do not change the tests.

## Input

- `localagent/units/U<N>/spec.md` — what to build.
- The unit's failing tests (from test-implement) — your concrete target.
- Optional: `codegraph explore "<topic>"` if `.codegraph/` exists, to locate the code to edit.

## Output

- Production code in the project's normal source tree that satisfies the spec and makes the unit's tests pass.
- Run the tests until **green**. Run the project's lint/typecheck too if it has them.

## Rules

- Implement the spec — not more (YAGNI), not less. Every acceptance condition in the spec must hold.
- **Never edit the tests to make them pass.** If a test looks wrong, `ESCALATE` — do not "fix" it.
- Match the existing code's style, patterns, and conventions. Keep changes within this unit's scope.
- Use real paths; never invent modules. Build on prior units via the interfaces in their spec/STATE.md.
- Stuck after ~5 distinct attempts on the same failure → `ESCALATE`, don't grind.

## Return

`DONE <changed paths> (green: all unit tests pass)` — or `ESCALATE <reason>` (spec wrong/ambiguous, or a test appears incorrect) — or `BLOCKED <reason>` (missing dependency, credential, or external service).
