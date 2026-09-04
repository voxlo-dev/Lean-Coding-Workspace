---
name: checkpoint
description: "Use to end a chat at a phase boundary rather than let compaction hit mid-work: writes CHECKPOINT.local.md, an untracked handout the next chat reads and deletes. Runs at the end of open-sprint, dynamic-workflow, close-sprint and project-initialiser, or on request. Invokable directly or via /checkpoint."
---

# Checkpoint

At a phase boundary a context reset is cheap — the next phase reads other files anyway — while compaction hits mid-work and summarises everything generically. So: hand over a few lines, then start a fresh chat.

**Write last**, after the phase's final commit: `CHECKPOINT.local.md` in the repo root, overwritten, English. It stays untracked (`project-initialiser` owns the gitignore) and the project's instruction file — the shim `project-initialiser` installs from `{home}/adapter/project/`, where the target needs one — imports it whole into the next session, which is why it stays short.

## Content

**Max 25 lines, fewer is better.** Three blocks:

- **Frame** — sprint slug, branch, phase just finished. One line.
- **Pick up here** — the next action, plus the paths worth opening first.
- **The user's own words** — every message of theirs carrying feature substance that no file states: scope calls, answers to questions asked this chat, a preference dropped in passing. **Quote verbatim**, and never filter by "the next chat can just ask" — making the user answer twice is the drift this stops. Doesn't count against the cap.
- **Chat-only residue** — why the run deviated from its spec, dead ends not worth retrying, open questions.

**Out: anything a surviving file already says** — board, tickets, spec, docs, commit messages. Link the path, never paraphrase. A fact with a durable home goes there first (decision → `docs/decisions.md`, learning → `maintain-memory`, later work → a ticket) and is not repeated here.

Applied honestly the last block is often empty, the user's words rarely. Nothing left at all → write nothing and say so; a handout restating files is pure cost.

Finally, set the chat title where the selected harness exposes that capability; otherwise skip it. Then tell the user to continue in a **new chat**; the handout only pays off if the context resets.
