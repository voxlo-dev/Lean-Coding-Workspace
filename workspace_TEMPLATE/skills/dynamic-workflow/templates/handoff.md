# Handoff — package {n}: {name}

The dispatch prompt for one implement package (template:
`~/.agents/skills/dynamic-workflow/templates/handoff.md`). **Written from the spec, never from
reading the code**, self-contained: the subagent starts blank. Not a file — fill it and send it.

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
- ~5 failed attempts on the same obstacle → return blocked. Do not grind.

## Return

Commit the package, then report in a few lines: what you did · each acceptance criterion met or not ·
test state · concerns and anything the orchestrator must decide. **The report is all that gets read —
no diffs.**
