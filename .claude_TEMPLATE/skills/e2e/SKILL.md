---
name: e2e
description: "Optional e2e stage of dynamic-workflow: validate a UI feature end-to-end. Runs existing automation, else grows a driver script step by step — agentic driving only for steps a script can't do. Consumes the e2e test-case file spec-design produced."
---

# E2E

Validate a UI feature end-to-end as cheaply as possible: **the script is the tester, the agent is the exception.** Input is the test case `spec-design` wrote (`artefacts/{sprint}/e2e_{feature}.md`) — missing → ask the user (write one now / skip e2e / verify manually).

A red step is a **product bug** → back to implementation (`dynamic-workflow` step 2). A red step caused by a selector, a wait or a fixture is a **script bug** → fix the script, don't touch the product.

1. **Automation already runnable?** — run it. Green → step 7. Red → triage per the rule above.
2. **Driver** — pick the framework the project has (`references/drivers.md` for what each one gives you). The **first** e2e run in a project configures it once and records the invocation in `docs/dev.md`; later runs just use it. No driver and none installable → every step is an agent step (step 5).
3. **Classify each test-case step** `auto` (default) or `agent`. `agent` only for: not scriptable (other app, system dialog, notification, camera, store flow, physical device) · qualitative (does it *look* right, does it feel janky). Anything an assertion can decide is `auto`, always.
4. **Grow the script, one step at a time** — append a step, rerun the script whole from the top, read stdout. Never weigh up whether scripting is worth it: red → fix → rerun is the normal path, and reruns are free by script and full price by agent, so it pays for itself inside this run. Read the text reporter only; screenshots exist for failures you can't read your way out of, the HTML report is never opened.
5. **Agent steps** — drive through the project's MCP server if one is present, otherwise write the user the exact steps to perform and take their result. Capture a screenshot only where the check is visual.
6. **Persist or discard** — the script always gets written; only its home is a decision:
   - **persistent** (flow stays in the product) → the project's test tree, committed, wired into the suite.
   - **temporary** (throwaway or interim UI) → `test-dump/`, gitignored, dies with the sprint. Automating a UI that won't exist next sprint still beats driving it by hand today.
7. **Report & board** — write `artefacts/{sprint}/e2e-report_{feature}.md` from `templates/e2e-report.md`. Green and verified → flip the ticket to `done` on the sprint board; a ticket the user still has to eyeball stays at `to test`.

## Artifacts

| What | Where |
| --- | --- |
| Test case (in) | `artefacts/{sprint}/e2e_{feature}.md` |
| Report (out) | `artefacts/{sprint}/e2e-report_{feature}.md` — distilled Markdown, frozen by `close-sprint` |
| Persistent script | project test tree, committed |
| Temporary script, screenshots, videos, traces, framework report output, logs | `test-dump/`, gitignored |

`test-dump/` is expendable: the report must stand on its own and may reference dump paths, never depend on them.
