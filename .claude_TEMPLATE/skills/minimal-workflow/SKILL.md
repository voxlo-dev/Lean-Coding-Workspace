---
name: minimal-workflow
description: "Use for a single, small, well-scoped change — a minor feature or a bugfix that needs no spec or planning."
---

# Minimal Workflow

Smallest possible ceremony: no spec, no planning, no other workflow skills. Just ship the change.

1. **Start immediately** — locate and understand the relevant code (codegraph if the repo is indexed, otherwise a targeted search), then implement. If the change leans on a third-party library/framework/API, look up current usage via **context7** instead of trusting recall.
2. **Test, if the project has a test setup** — prefer automated: find an existing test covering the change, else write one or extend the closest. No test setup at all → skip to step 4 and hand off for manual verification.
3. **Loop to green.**
4. **Minimal docs & memory maintenance** — the essentials inline, skills uninvoked; the large workflow runs catch the drift. **Changed behaviour without a spec?** One line into the sprint file's **Behaviour context** — `close-sprint` folds that into `docs/behaviour.md`.
5. **Commit.**
6. **Board, only if a ticket drove this** — flip its status to `to test` on the sprint board. A fix done on the spot needs no ticket; anything left undone becomes one in `backlog/`.

**Hand off when automation isn't worth it** — a non-trivial, very niche change, or a test that's hard to write (no framework, hard-to-test feature): pause and let the user verify before committing. Weigh manual testing against the cost of automating.