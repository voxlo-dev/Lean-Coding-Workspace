# Handoff — package {n}: {name}

The dispatch prompt for one implement package (template:
`~/.claude/skills/dynamic-workflow/templates/handoff.md`). **Written from the spec, never from
reading the code**, and self-contained: the subagent starts blank. Not a file — fill it and send it.

- **Spec:** `artefacts/{sprint}/spec_{feature}.md` (read it; it is your anchor if you compact)
- **Ticket:** {`T-NNN`, or the ticket this package serves}
- **Goal:** {the one outcome this package produces}
- **Touches:** {exact files, interfaces, symbols — and what is out of bounds}
- **Testing:** {preset} → {what this package owes: tests written where, which existing test file they extend, or the failing tests it turns green}
- **Acceptance criteria:** {the spec's criteria for this package, verbatim — you are judged against these}
- **Context:** {the spec slices / prior report lines it needs, and nothing else}

## Standing rules

- **Foreground every run.** Build, tests, scripts — wait them out and read the output; where a tool
  forces the background, poll to exit before reporting. A turn ended on a pending run is a failure.
- **Third-party library, framework or API → look it up via context7**, never from recall.
- **Tests are read-only** unless this package writes them. A test that is wrong because the
  *signature* is wrong → change signature and test together and say so; bending an assertion to fit
  the code is never the fix.
- **Stay in the package.** Something worth doing outside it goes into the report, not into the diff.
- Reuse the existing abstraction over adding a parallel one.
- ~5 failed attempts on the same obstacle → return blocked. Do not grind.

## Return

Commit the package, then report in a few lines: what you did · each acceptance criterion met or not ·
test state · concerns and anything the orchestrator must decide. **The report is the only thing read
— no diffs.**
