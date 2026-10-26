# Threads

How this harness starts a thread: a session of its own the user works in, reporting back here.
Read by `open-sprint` step 4. Only the desktop app has the tool — absent from this session → no threads.

- **Start:** `start_session` (load it via `ToolSearch` when deferred) · `initiation: user_asked` ·
  `context: fresh` · `use_worktree: false` · `wake_when_done: true` · `title` · `prompt` = the
  pointer · `background` ≥80 chars: sprint, branch, tickets. Same host and folder as this session.
- **Only a session the user drives starts one**; a thread cannot start another.
- **Back here:** a note per thread turn, quoting its closing message's first 400 chars. A verdict
  as its first word ends the thread; a note without one is the thread waiting on the user — ignore it.
- **Follow-up:** `SendMessage` to the thread's `local_…` id. Link it to the user as `[title](#local_…)`.
- **One thread at a time** — they share this checkout and its branch.
