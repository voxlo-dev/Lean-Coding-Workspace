# Run brief — pipeline row {n}: {workflow} on {T-NNN, …}

The dispatch brief for one pipeline run (template:
`~/.agents/skills/sprint-orchestrator/templates/run-brief.md`), written by the conductor to
`.temp/dispatch/{sprint}-{n}/brief.md`. Paths, never inlined content.

- **Working directory:** {absolute repo path} · branch `{sprint-slug}`
- **Invoke:** `{workflow}` on {`T-NNN`, …}
- **Pipeline row:** `artefacts/{sprint}/sprint.md` → Pipeline row {n} · mode {auto | review} · strategy {preset · e2e yes/no · delegation | —}
- **Inputs:** {ticket paths · mockup paths · `artefacts/{sprint}/sprint-decisions.md`}
- **Constraints:** {standing constraints from the user that no file states — else none}

## Standing rules

- **Nobody to ask but the conductor.** Wherever the workflow would ask, confirm or pause for the
  user — a question, a review, a verification, the ~5-attempt escalation — instead commit what
  holds, write the question to `report.md` (question · options · your recommendation · state so
  far) and end with `NEEDS_DECISION`. The conductor resumes this session with the answer in
  `answer-{k}.md` beside this brief. Never guess past a question the sprint left open.
- **Already decided:** the pipeline row is the spec's testing preset, e2e and delegation; the run is
  autonomous and dispatches in-harness only. UI = the mockups linked in the tickets — build against
  them, design nothing new.
- **Mode `auto`:** `spec-design` in pipeline mode, lean spec, no review. **Mode `review`:** commit the
  spec, then `NEEDS_DECISION` with its path as the question.
- Your subagents escalate to you per the workflow; only what you cannot decide goes up.
- Stay on the branch — never switch, merge or push. No checkpoint: the run ends with this process.

## Return

`report.md` beside this brief: what was done · commits · tickets moved on the board · what the user
must verify by hand · concerns. Last stdout line, alone: `DONE` · `NEEDS_DECISION` · `BLOCKED <reason>`.
