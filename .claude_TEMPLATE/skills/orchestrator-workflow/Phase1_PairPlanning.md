# Phase 1 — Pair Planning

> Run **inline** by the dispatcher — NOT as a subagent. This phase is interactive; the dispatcher talks directly with the user.

## Goal

Produce `<repo>/workflowArtifacts/PLAN.md` and get explicit user approval before Phase 2.

## Step 1: Bootstrap

The project memory (and any imported domain/global memory) is already loaded — consult it for relevant context.

Check `<repo>/workflowArtifacts/` for an existing `PLAN.md`:
- Found and the task implies continuation → summarise it inline, ask: "Continue from this plan, update it, or start fresh?"
- Missing or the task implies a new project → go to Step 2.

## Step 2: Discovery Gate

Check whether `BUILD_SPEC_<name>.md` exists in `<repo>/workflowArtifacts/` AND passes the completeness check.

**Completeness check — BUILD_SPEC must have all of:**
- Section 1: Project Overview (non-empty)
- Section 2: Scope and Deliverables
- Section 5: Component Map (≥1 component)
- Section 9: Work Package Breakdown (≥1 WP with scope and ACs)
- An accompanying `USER_STORIES.md` with ≥1 story

**If incomplete or missing → ground the plan via discovery:**
- Repo is codegraph-indexed (`.codegraph/` exists) → query codegraph directly (`codegraph explore "<task topic>"`) to understand the relevant structure. Do not spawn an Explore subagent for what the graph knows.
- Not indexed → do a brief targeted explore with built-in tools, scoped to the task. Don't index the repo yourself — that's the user's decision.
- New / empty project → skip discovery.

**If complete → not a greenfield run:** load the existing BUILD_SPEC + USER_STORIES.md for context; treat this as an update/extension.

## Step 3: Pair Planning Dialogue

Ask the user targeted questions based on what you now know (from discovery or the existing spec). Keep it tight — 2–3 rounds max. Never ask for something you already have.

**For new projects (ask only what's still unknown):**
- Goal and who benefits? (target users, core problem)
- Must-haves (P0) vs nice-to-haves (P2)?
- Hard constraints? (tech stack, APIs, deadlines, access)
- What does "done" look like? (observable success state)
- Known risks or tricky areas?

**For updates/extensions:**
- Which existing WPs / user stories are affected?
- Are any existing acceptance criteria changing?
- New WPs or stories needed?
- Any new constraints?

Do NOT ask about testing levels — those come from the dispatcher's Configuration.

## Step 4: Write PLAN.md

Write `<repo>/workflowArtifacts/PLAN.md`:

```markdown
# Plan: [Project Name]
Generated: [date]
Task: [one-line task summary]

## User Story Sketch

1. As a [role], I want [action] so that [outcome].
2. ...

(W2 expands these into full USER_STORIES.md with acceptance criteria)

## Tech / Approach Decisions

- [key decision + rationale]

## Constraints

- [hard limits that must not be violated]

## Discovery Grounding

[Only if discovery ran — key structural findings that shape the plan:]
- [finding from codegraph / explore relevant to this task]

[Omit this section if no discovery ran]

## Work Package Sketch

| WP | Title | Scope summary | Depends on |
| --- | --- | --- | --- |
| WP1 | | | — |
| WP2 | | | WP1 |

## Open Questions

- [anything unresolved that must be answered before implementation]
- [leave empty if none]
```

## Step 5: User Approval Gate

Show the user inline:
- User story sketch (one line each)
- WP sketch table
- Path to PLAN.md

Ask explicitly:

> **"Does this plan look right? Say 'approved' to proceed to spec writing, or tell me what to change."**

Do NOT proceed to Phase 2 until explicit approval. Silence is not approval. If changes are requested: update PLAN.md and re-show the summary. Repeat until approved.

## Completion Signal

Once approved, the dispatcher proceeds to Phase 2 with:
- `PLAN_APPROVED` + PLAN.md path
- Whether this was a greenfield or update run
- Whether codegraph is available for the spec architect
