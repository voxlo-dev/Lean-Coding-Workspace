---
name: checkpoint
description: "Use to end a chat at a phase boundary rather than let compaction hit mid-work: writes CHECKPOINT.local.md, an untracked handout carried across chats until worked off or the sprint closes. Runs only at the end of open-sprint, dynamic-workflow and project-init — never while a sprint-autopilot pipeline is active — or on the user's request. Invokable directly or via /checkpoint."
---

# Checkpoint

At a phase boundary a context reset is cheap — the next phase reads other files anyway — while compaction hits mid-work and summarises everything generically. So: hand over a few lines, then start a fresh chat — at the boundaries the description names or on request, never on your own initiative. **Sprint file with a Pipeline row still `pending` or `running`?** Then only the user's request checkpoints — neither `sprint-autopilot` nor any skill it runs, inline or dispatched.

**Write last**, after the phase's final commit: `CHECKPOINT.local.md` in the repo root, English. **An existing one is merged, not overwritten** — its still-open items stay, settled ones drop. It stays untracked (`project-init` owns the gitignore) and the project's instruction file — the shim `project-init` installs from `{home}/adapter/project/`, where the target needs one — imports it whole into the next session, which is why it stays short.

## Content

**Around 25 lines, 50 at most.** Four blocks:

- **Frame** — sprint slug, branch, phase just finished. One line.
- **Pick up here** — the next action, plus the paths worth opening first.
- **Chat summary** — a few terse lines condensing the exchange with the user wherever it carried feature substance no file states: scope calls, answers to questions asked, a preference dropped in passing. Never filter by "the next chat can just ask" — making the user answer twice is the drift this stops.
- **Chat-only residue** — why the run deviated from its spec, dead ends not worth retrying, open questions.

**Out: anything a surviving file already says** — board, tickets, spec, docs, commit messages. Link the path, never paraphrase. A fact with a durable home goes there first (decision → `docs/decisions.md`, learning → `maintain-memory`, later work → a ticket) and is not repeated here.

Applied honestly the last block is often empty, the summary rarely. Nothing left at all → write nothing and say so; a handout restating files is pure cost.

Finally, set the chat title where the selected harness exposes that capability; otherwise skip it. Then tell the user to continue in a **new chat**; the handout only pays off if the context resets.

**Reading it:** act on it, strike what got settled, and delete the file only once nothing is left — important points ride along until sprint end, where `close-sprint` deletes it regardless.
