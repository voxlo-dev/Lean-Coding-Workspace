---
name: e2e
description: "Optional e2e stage of dynamic-workflow: validate a UI feature end-to-end. Runs existing automation, or drives the test once (via an MCP server or a delegated agent) and then writes the automation. Consumes the e2e test-case file spec-design produced."
---

# E2E

End-to-end validation for a UI feature. The goal is two things at once: a **green e2e
run** and **repeatable automation** for next time. The test case is the separate Markdown
file `spec-design` wrote (`artefacts/{sprint}/e2e_{feature}.md`, seeded from `spec-design`'s
`templates/e2e-testcase.md`).

This skill ships its own template in `templates/` — `e2e-run.md`. Seed a run log from it
(`artefacts/{sprint}/e2e-run_{feature}.md`) whenever you drive the test, recording observed vs. expected
per step.

1. **Test case available?** — no e2e test-case file → ask the user what to do (write one now / skip e2e / verify manually). Don't invent a test case silently.
2. **Automation already runnable?** — a coded, runnable e2e test exists → run it.
   - **Green → done.**
   - **Red → back to implementation** (`dynamic-workflow` step 2).
3. **No automation yet — drive the test once, by what's available, logging to `e2e-run_{feature}.md`:**
   - **e2e framework + MCP server present** → dispatch an **e2e subagent**: execute the test-case steps through the MCP server and fill the run log.
   - **otherwise** → delegate to the user: recommend running it manually or via a browser / computer-use agent, and write them the exact prompt / steps to follow; capture the result in the run log.
4. **Automate it** — from the concrete passing run, write the e2e automation so future runs are repeatable. A red run here also sends you back to implementation.
5. **Board** — green and verified → flip the ticket's status to `done` on the sprint file's board. That status is what `close-sprint` distils and dissolves; a ticket the user still has to eyeball stays at `to test`.
