---
name: checkpoint
description: "Use to end a chat at a phase boundary rather than let compaction hit mid-work: writes CHECKPOINT.local.md, an untracked handout the next chat reads and deletes. Runs at the end of open-sprint, dynamic-workflow, close-sprint and project-initialiser, or on request. Invokable by Claude or via /checkpoint."
---

# Checkpoint

A phase boundary is where a context reset is cheap: the next phase reads different files anyway. Compaction instead hits mid-implementation and summarises the whole transcript generically. So: hand over a few targeted lines, then start a fresh chat.

**Write it last** — after the phase's final commit. `CHECKPOINT.local.md` sits in the repo root and must be **untracked**: the project `.gitignore` needs `*.local.md` (add the line if an older project lacks it), otherwise the handout lands in a commit and dirties the tree. One file, overwritten. English, like every other handoff.

## What goes in

Three blocks, **25 lines total**:

- **Frame** — sprint slug, branch, which phase just finished. One line.
- **Pick up here** — the single next action, plus the paths worth opening first (spec, sprint file, ticket).
- **Chat-only residue** — what existed nowhere but in the conversation: user preferences and calls stated in passing, why the run deviated from its spec or plan, dead ends not worth retrying, questions still open.

## What stays out

**Anything a surviving file already says** — board status, ticket text, spec content, doc contents, commit messages. Link the path, never paraphrase it. A fact with a durable home goes there *first* and is then not repeated here: decision → `docs/decisions.md` + `sprint-decisions.md`, learning → `maintain-memory`, work not being done now → a ticket.

Applied honestly this often leaves nothing. Then write nothing and say so — an empty handout is the healthy case, a handout restating files is pure cost.

## Close out

Tell the user the checkpoint is written and the next step is a **new chat** — the handout only pays off if the context actually resets.
