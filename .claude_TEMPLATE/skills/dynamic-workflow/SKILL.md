---
name: dynamic-workflow
description: "Use for feature work or any change that warrants a spec. spec-design decides the test and implementation strategy once; this pipeline then executes them in a fixed order — implement, optional e2e, docs, memory, PR. Trivial one-liners use minimal-workflow."
---

# Dynamic Workflow

1. **Spec** — invoke `spec-design`. It brainstorms, designs the UI if there is one, decides the **test strategy** and the **implementation strategy**, and writes the spec. Review, then commit the spec.
2. **Implement** — for each implement package in the spec, run the **strategy the spec chose**, then commit per package (optional review first):
   - `direct` → implement inline: locate the code, write it, if available, run tests to green.
   - `TDD` → `superpowers:test-driven-development` (only when the test strategy is full-TDD).
   - `subagent driven` → `superpowers:subagent-driven-development` for independent packages.
   The strategy is **fixed**. The one sanctioned deviation: a package that fails or turns buggy → `superpowers:systematic-debugging`, then resume the chosen strategy.
3. **e2e** (optional) — only if the spec's test strategy includes e2e → invoke `e2e`. A red result sends you back to step 2.
4. **Docs** — invoke `maintain-docs`, then commit.
5. **Memory** — invoke `maintain-memory`.
6. **Open a pull request.**

**Stuck? Escalate.** On a technical problem, pause and ask the user after ~5 solution
attempts (an attempt = a new approach via a tool call) — don't grind.
