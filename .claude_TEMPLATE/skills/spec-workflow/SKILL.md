---
name: spec-workflow
description: "Use for feature work on an existing project that needs a spec and several work packages. Orchestrate on Opus/Fable, delegate implementation to Sonnet subagents."
---

# Spec Workflow

Main thread runs the orchestration model (start on Opus/Fable or use `opusplan`); each work package is delegated to a **Sonnet subagent**.

Work packages are **phases**, not vertical slices: write the tests once, implement until green, optionally run e2e, then docs. Commit after every package.

1. **Brainstorm** with the user — invoke superpowers' brainstorming skill; do not reinvent it.
2. **Spec** — copy `docs/specs/SPEC_TEMPLATE.md` to a new spec file under `docs/specs/`, then fill it. The packages are the fixed phases below; the only thing you size is how many **implement** packages the spec needs (≥1). If the feature has a UI, invoke `ui-design` to lay out this feature's mockup against the styleguide and capture it in the spec's UI section.
3. **Set the autonomy mode** — ask the user up front, with a recommendation: (a) fully autonomous or pause for review after each package, and (b) dispatch a Sonnet subagent per package or run the packages directly in the main thread (recommend direct for a small spec, subagents for a large one). Follow both for the whole run.
4. **Run the packages in order**, each as a Sonnet subagent (or directly, per step 3), and commit after each:
   1. **Test package** — write all tests for the spec in one pass (unit/integration + e2e). Sensible test flows across components, no redundancy; merge with existing tests rather than duplicating; **core** coverage only (aim for fewer lines of test than production). e2e tests are written here but not run yet.
   2. **Implement package(s)** — at least one. Write the production code and run unit tests until green. Split into several implement packages when the spec is large; otherwise one.
   3. **e2e package (optional)** — only if the project has a UI and an e2e framework in place. Run the e2e tests from the test package and fix until green. If there's no e2e available, hand off to the user to verify; on their OK, continue to docs.
   4. **Docs package** — invoke `maintain-docs` for all sensible doc changes in one pass.
   - If not autonomous, pause for user review after each package.

   **Handoff:** brainstorming already loaded the relevant files into context — write each subagent's handoff from that, not from scratch. Detailed enough that the subagent need not re-read everything, but not so detailed that writing it costs more than just implementing. Tell it which files to re-read and which it can safely skip.
5. **Stuck? Escalate.** On a technical problem, pause and ask the user after ~5 solution attempts (an attempt = a new approach via a tool call) — don't grind.
6. **Persist memory** — invoke `maintain-memory` to save decisions, rationale, and gotchas to the right scope, prune stale entries, and spin off a reusable skill if one emerged.
7. **Open a pull request.**
