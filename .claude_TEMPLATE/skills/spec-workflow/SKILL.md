---
name: spec-workflow
description: "Use for feature work on an existing project that needs a spec and several work packages. Orchestrate on Opus/Fable, delegate implementation to Sonnet subagents."
---

# Spec Workflow

Main thread runs the orchestration model (start on Opus/Fable or use `opusplan`); implementation is delegated to **Sonnet subagents**.

1. **Brainstorm** with the user — invoke superpowers' brainstorming skill; do not reinvent it.
2. **Spec** — copy `docs/specs/SPEC_TEMPLATE.md` to a new spec file under `docs/specs/`, then fill it. Break the work into work packages (WPs), each independently implementable and testable. A simple, small plan can be a **single WP** — don't force a split.
3. **Set the autonomy mode** — ask the user up front: fully autonomous, or pause for review after each WP. Follow that for the whole run.
4. **Per work package** — dispatch a Sonnet subagent per WP. For a single, simple WP, skip the subagent and implement directly. Each subagent:
   1. Writes tests first — sensible **core** coverage only; skip trivial tests (aim for fewer lines of test code than production code).
   2. Implements.
   3. Runs unit + UI tests until green.
   4. Invokes `maintain-docs`.
   5. Commits.
   - If not autonomous, pause for user review after the WP.

   **Handoff:** brainstorming already loaded the relevant files into context — write the handoff from that, not from scratch. Detailed enough that the subagent need not re-read everything, but not so detailed that writing it costs more than just implementing. Tell it which files to re-read and which it can safely skip.
5. **Stuck? Escalate.** On a technical problem, pause and ask the user after ~5 solution attempts (an attempt = a new approach via a tool call) — don't grind.
6. **Persist memory** — invoke `maintain-memory` to save decisions, rationale, and gotchas to the right scope (and prune stale entries). If a reusable procedure emerged, create a skill with superpowers' **skill-creator** — decide its scope and place it accordingly:
   - global → `~/.claude/skills/{skill-name}/` (must be a **direct** child of `skills/`; grouping subfolders aren't discovered)
   - domain → the domain master under `~/.claude/domains/{x}-domain/`
   - project → the repo's `.claude/skills/`

   A brand-new skill *folder* is usually discovered only on the next session — flag this to the user.
7. **Open a pull request.**
