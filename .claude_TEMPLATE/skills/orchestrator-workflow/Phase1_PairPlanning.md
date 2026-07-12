# Phase 1 — Pair Planning

> Executed **inline** by the Dispatcher — NOT as a subagent. This phase is interactive; the Dispatcher talks directly with the user.

---

## Goal

Produce `docs/artefacts/{sprint}/plan_{feature}.md` and receive explicit user approval before Phase 2 starts.

---

## Step 1: Bootstrap

The project `MEMORY.md` (and any imported domain/global memory) is already loaded — consult it for prior decisions and gotchas relevant to this task. No gateway call.

Check `docs/artefacts/{sprint}/` for an existing `plan_{feature}.md`:
- Found and task implies continuation → summarize the plan inline, ask: "Continue from this plan, update it, or start fresh?"
- **A `plan`-skill *Lastenheft* lands at the same path (`plan_{feature}.md`).** If one exists, treat it as the requirements basis: read it, and in Step 3 only *confirm* its points rather than re-asking them from scratch.
- Missing or task implies new project → proceed to Step 2.

---

## Step 2: Auto-Discovery Gate

Check whether `spec-{feature}.md` exists in `docs/artefacts/{sprint}/` AND passes the completeness check below.

**Completeness check — BUILD_SPEC must have all of:**
- Section 1: Project Overview (non-empty)
- Section 2: Scope and Deliverables
- Section 5: Component Map (at least one component)
- Section 9: Work Package Breakdown (at least one WP with scope and ACs)
- Accompanying `user-stories_{feature}.md` with at least one user story

**Trigger condition (any of the following):**
- BUILD_SPEC file missing
- BUILD_SPEC missing one or more required sections
- `user-stories_{feature}.md` missing
- BUILD_SPEC clearly written for a different project (name mismatch)

**If triggered — ground the planning conversation via codegraph:**
- **Indexed repo** (`.codegraph/` exists) → query codegraph directly: `codegraph explore "<task-relevant symbols or question>"` (shell) or the `codegraph_explore` / `codegraph_node` MCP tools. This returns the relevant symbols' source plus the call paths between them — enough to locate the blast radius and reuse existing abstractions.
- **Not indexed** → spawn a brief `Explore` subagent scoped to the task area; receive a short structural summary. Do not index the repo yourself — that is the user's decision.

Use the structural findings to ground Step 3.

**If not triggered:**
Load existing BUILD_SPEC and user-stories_{feature}.md for context.
Note internally: this is an update/extension run, not a greenfield project.

---

## Step 3: Pair Planning Dialogue

Ask the user targeted questions based on what you now know (from codegraph discovery or the existing spec). Keep rounds tight — 2–3 rounds max. Do not ask for information you already have.

**Topics to cover (ask only what's still unknown):**

For new projects:
- What is the goal and who benefits? (target users, core problem)
- What are the must-haves (P0) vs nice-to-haves (P2)?
- Any hard constraints? (tech stack, APIs, deadlines, access/permissions)
- What does "done" look like? Observable success state.
- Any known risks or technically tricky areas?

For updates/extensions:
- Which existing WPs or user stories are affected?
- Are any existing acceptance criteria changing?
- New WPs or stories needed?
- Any new constraints introduced?

Do NOT ask about testing levels here — the Dispatcher already resolved them with the user at Bootstrap.

---

## Step 4: Write plan_{feature}.md

Write `docs/artefacts/{sprint}/plan_{feature}.md`:

```markdown
# Plan: [Project Name]
Generated: [date]
Task: [one-line task summary]

## User Story Sketch

1. As a [role], I want [action] so that [outcome].
2. ...

(W2 will expand these into full user-stories_{feature}.md with acceptance criteria)

## Tech / Approach Decisions

- [key decision + rationale]

## Constraints

- [hard limits that must not be violated]

## Discovery Grounding

[Only include if codegraph/Explore discovery ran — key structural findings that shape the plan:]
- [finding relevant to this task: existing module to extend, blast radius, reusable abstraction]

[Omit this section if no discovery pass ran]

## Work Package Sketch

| WP | Title | Scope summary | Depends on |
|---|---|---|---|
| WP1 | | | — |
| WP2 | | | WP1 |

## Open Questions

- [anything unresolved that must be answered before implementation]
- [leave empty if none]
```

---

## Step 5: User Approval Gate

Show the user inline:
- User story sketch (bullet list, one line each)
- WP sketch table
- Path to plan_{feature}.md for reference

Ask explicitly:

> **"Does this plan look right? Say 'approved' to proceed to spec writing, or tell me what to change."**

Do NOT signal the Dispatcher to proceed to Phase 2 until explicit approval is received.
Silence is not approval.

If changes are requested: update plan_{feature}.md and re-show the summary. Repeat until approved.

---

## Phase 1 Completion Signal

Once user approves: return to Dispatcher with:
- `PLAN_APPROVED` + plan_{feature}.md path
- Whether codegraph discovery ran (so W2 knows the structural basis)
- Note whether this was a greenfield or update run

Dispatcher proceeds to Phase 2.
