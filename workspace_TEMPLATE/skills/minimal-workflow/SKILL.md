---
name: minimal-workflow
description: "Use for a single, small, well-scoped change — a minor feature or a bugfix that needs no spec: a short interview and a plan in the chat, then ship."
---

# Minimal Workflow

Smallest possible ceremony: no spec, no files for the plan, no other workflow skills. Dispatched by `sprint-autopilot`, its run brief overrides every user touchpoint below — the ticket stands in for the interview, skip the plan, escalate only a genuine gap.

1. **Ground** — locate and understand the relevant code (codegraph if the repo is indexed, otherwise a targeted search) before asking anything.
2. **Interview, small** — only what the code and ask leave open: expected behaviour, scope edge, an ambiguous case. **≤3 questions, one at a time**, multiple-choice where possible; a clear ask needs none.
3. **Mini plan in the chat** — 3–6 bullets: what changes where, how it gets verified, what stays out. Get the user's nod; never written to a file. Implementation departs from it → say so, don't drift silently.
4. **Implement** — a change leaning on a third-party library/framework/API → current usage via **context7**, not recall.
5. **Test, if the project has a test setup** — prefer automated: find an existing test covering the change, else write one or extend the closest. No test setup at all → skip to step 7 and hand off for manual verification.
6. **Loop to green.**
7. **Minimal docs & memory maintenance** — the essentials inline, skills uninvoked; the large workflow runs catch the drift. **Changed behaviour without a spec?** One line into the sprint file's **Behaviour context** — `close-sprint` folds that into `docs/behaviour.md`.
8. **Commit.**
9. **Board, only if a ticket drove this** — flip its status to `to test` on the sprint board. A fix done on the spot needs no ticket; anything left undone becomes one in `backlog/`.

**Hand off when automation isn't worth it** — a non-trivial, very niche change, or a test that's hard to write (no framework, hard-to-test feature): pause and let the user verify before committing. Weigh manual testing against the cost of automating.
