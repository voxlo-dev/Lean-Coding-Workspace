---
name: minimal-workflow
description: "Use for a single, small, well-scoped change — a minor feature or a bugfix that needs no spec or planning. Invoke explicitly."
disable-model-invocation: true
---

# Minimal Workflow

Smallest possible ceremony — no spec, no other workflow skills, no planning. Just ship the change.

1. **Start immediately** — use codegraph to locate and understand the relevant code, then implement. No brainstorming, no spec.
2. **Test, never by hand** — find an existing test that covers the change; if none fits, write one or extend the closest test. Never verify manually.
3. **Loop to green** — implement and run the test until it passes.
4. **Hand off when automation isn't worth it** — pause and let the user verify before committing when either the change/test is non-trivial, or writing the test itself is hard (no suitable framework, hard-to-test feature). Weigh user-testing against the cost of automating; when manual is clearly faster, hand off.
5. **No maintain-docs** — but if something reusable was learned, persist it to claude-mem.
6. **Commit.**
