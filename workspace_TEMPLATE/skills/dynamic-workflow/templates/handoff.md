# Handoff — package {n}: {name}

The dispatch prompt for one implement package (template:
`~/.agents/skills/dynamic-workflow/templates/handoff.md`). **Written from the spec, never from
reading the code**, self-contained: the subagent starts blank. In-harness it is filled and sent as
the prompt; dispatched out of process it is written to `.temp/dispatch/{package}/brief.md` and the
command names that path — same content either way.

- **Spec:** `artefacts/{sprint}/spec_{feature}.md` — read it; your anchor if you compact
- **Ticket:** {`T-NNN` this package serves}
- **Goal:** {the one outcome this package produces}
- **Touches:** {exact files, interfaces, symbols — and what is out of bounds}
- **Testing:** {preset} → {tests written where, which existing file they extend, or the failing tests this turns green}
- **Acceptance criteria:** {the spec's criteria, verbatim — you are judged against these}
- **Context:** {the spec slices / prior report lines it needs, nothing else}

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

## Return

Commit the package, then report in a few lines: what you did · each acceptance criterion met or not ·
test state · concerns and anything the orchestrator must decide. Blocked → report all the same,
naming the obstacle, what was tried and what you need. **The report is all that gets read —
no diffs.** Dispatched out of process, it goes to the `report.md` this brief names, and the run's
last line is the verdict alone.
