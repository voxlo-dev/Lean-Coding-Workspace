---
name: minimal-workflow
description: "Use for a single, small, well-scoped change — a minor feature or a bugfix that needs no spec or planning."
---

# Minimal Workflow

Smallest possible ceremony — no spec, no other workflow skills, no planning. Just ship the change.

1. **Start immediately** — locate and understand the relevant code (via codegraph if the repo is indexed, otherwise a targeted search), then implement. No brainstorming, no spec. If the change leans on a third-party library/framework/API, look up current usage via **context7** instead of trusting recall.
2. **Test, if the project has a test setup** — prefer automated tests: find an existing test that covers the change; if none fits, write one or extend the closest test. If the project has no test setup at all, skip to step 4 and hand off for manual verification.
3. **Loop to green** — implement and run the test until it passes.
4. **Minimal docs & memory maintenance** — only the most essential changes, do not invoke the skill, drifts are catched by large workflow runs.
5. **Commit.**
6. **Board, only if a ticket drove this** — flip its status to `to test` on the sprint file's board. A fix done on the spot needs no ticket at all; something you *won't* do now goes into `backlog/` as one.

**Hand off when automation isn't worth it** — when the change/test is non-trivial, very specific/niche or the test is hard to write (no framework, hard-to-test feature), pause and let the user verify before committing. Weigh manual testing against the cost of automating; hand off when manual is clearly faster.