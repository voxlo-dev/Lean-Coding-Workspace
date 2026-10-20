# Handoff — package {n}: {name}

Template: `~/.agents/skills/dynamic-workflow/templates/handoff.md` — filled per `dynamic-workflow`'s loop.

- **Spec:** `artefacts/{sprint}/spec_{feature}.md` → **package {n}** — read **Strategy**,
  **Components** and your package entry, whose acceptance criteria you are judged against; the rest
  only where those point. Your anchor if you compact.
- **From earlier reports:** {what this package depends on that the spec doesn't say — a moved
  signature, a deviation, a helper to reuse — else none}
- **Failing tests to turn green:** {light-tdd only — your slice of package 1's red list; else drop}
- **Narrowed:** {re-dispatch only — the narrowed brief and the obstacle from the last report; else drop}

## Standing rules

- **Foreground every run** — build, tests, scripts: wait them out and read the output. A turn ended
  on a pending run is a failure.
- **Third-party library, framework or API → look it up via context7**, never from recall.
- **Tests are read-only** unless this package writes them. One exception: a test wrong because the
  *signature* is wrong → change signature and test together and say so. Bending an assertion to fit
  the code never is.
- **Stay in the package** — anything worth doing outside it goes into the report, not the diff.
- Reuse the existing abstraction over adding a parallel one.
- **~5 failed attempts on the same obstacle → return blocked**, a rerun of a flaky test counting as
  an attempt. Never resolve it yourself — no ticket, no task, no suggestion: only the report reaches
  the orchestrator, who alone decides retry-or-ticket.
- **Escalation limits: ~300k tokens · 1 h · 100 tool calls.** Reaching any → commit what holds and
  return a `limit` report (done so far, what's left); the orchestrator decides whether you go on.

## Return

Commit the package, then report in a few lines: what you did · each acceptance criterion met or not ·
test state · concerns and anything the orchestrator must decide. Blocked → report all the same,
naming the obstacle, what was tried and what you need. **The report is all that gets read —
no diffs.** In-harness it is your return message, nothing on disk; dispatched out of process it goes
to `report.md` beside this brief and your last line is the verdict alone.
