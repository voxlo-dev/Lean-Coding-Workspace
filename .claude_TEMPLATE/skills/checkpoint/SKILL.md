---
name: checkpoint
description: "Use to end a chat at a phase boundary rather than let compaction hit mid-work: writes CHECKPOINT.local.md, an untracked handout the next chat reads and deletes. Runs at the end of open-sprint, dynamic-workflow, close-sprint and project-initialiser, or on request. Invokable by Claude or via /checkpoint."
---

# Checkpoint

At a phase boundary a context reset is cheap — the next phase reads other files anyway — while compaction hits mid-work and summarises everything generically. So: hand over a few lines, then start a fresh chat.

**Write last**, after the phase's final commit: `CHECKPOINT.local.md` in the repo root, overwritten, English. It stays untracked (`project-initialiser` owns the gitignore) and the project `CLAUDE.md` imports it whole into the next session, which is why it stays short.

## Content

**Max 25 lines, fewer is better.** Three blocks:

- **Frame** — sprint slug, branch, phase just finished. One line.
- **Pick up here** — the next action, plus the paths worth opening first.
- **Chat-only residue** — what lived nowhere but in the conversation: calls the user made in passing, why the run deviated from its spec, dead ends not worth retrying, open questions.

**Out: anything a surviving file already says** — board, tickets, spec, docs, commit messages. Link the path, never paraphrase. A fact with a durable home goes there first (decision → `docs/decisions.md`, learning → `maintain-memory`, later work → a ticket) and is not repeated here.

Applied honestly this often leaves nothing. Then write nothing and say so — a handout restating files is pure cost.

Finally, set the chat title to what the chat actually did — `mcp__ccd_session_mgmt__set_session_title`, present only in the Claude desktop app, skip where it's absent. Then tell the user to continue in a **new chat**; the handout only pays off if the context resets.
