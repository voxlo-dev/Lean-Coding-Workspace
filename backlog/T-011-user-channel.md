# T-011 — User channel module over Telegram

- **Summary:** optional module that lets every dispatched run and subagent notify and ask the user in one Telegram chat, with answers routed back to the run that asked
- **Category:** feature
- **Importance:** medium
- **Effort:** M
- **Depends on:** none — `T-001` consumes it

## Why

A dispatched run has no user: headless harnesses disable their question tools, and a conductor
relaying every `spec-design` question is a round trip per question through the one context the
design keeps small. The user wants OpenClaw-style interaction — status and questions on the phone,
answers from there — without adopting OpenClaw, which would replace the conductor with its own
platform, model and daemon.

## What

- **One private chat** with one bot. Every message is tagged `[{project}/{run} · {role}]`.
- **Any process may notify and ask** — conductor, dispatched runs, in-harness subagents.
- **Answers route to the asker** — a question id in the inline buttons' `callback_data`, or a
  Telegram reply to the question message; free text allowed.
- **Harness-neutral** — reachable from any harness's shell, no per-harness setup beyond install.
- **Optional** — absent, every workflow behaves exactly as today. The ask rule reaches a run through
  its dispatch brief, never through the always-loaded `AGENTS.md`.
- **Long waits park** — past a bound, the run ends with `NEEDS_DECISION` and resumes later instead
  of holding a process and a context open for hours.

Settled 2026-09-28: own broker over OpenClaw · one private chat over a group with a topic per run.

## Constraints found — docs, source and issues, 2026-09-28, unprobed

- **Sending is free, receiving is exclusive.** `sendMessage` is stateless HTTP, so any number of
  processes can post into the same chat. Telegram allows **exactly one `getUpdates` consumer per
  token** (or one webhook) — hence a single local broker owning the poll, and everyone else talking
  to it.
- **No off-the-shelf fit.** The official Claude Code Telegram channel
  (`claude-plugins-official/external_plugins/telegram`) is two-way with permission relay, but
  Claude-Code-only, research preview, claude.ai/Console auth (no local model), `--channels` only —
  and it kills any other poller on its token at start. Human-in-the-loop MCP servers
  (mcp-kilo-telegram, mcp-communicator-telegram, …) poll per instance over stdio, so two concurrent
  runs steal each other's answers. OpenClaw + openclaw-code-agent does the whole flow but is a
  conductor replacement.
- **Blocking waits hit tool timeouts** — a blocking MCP `ask` is capped by each harness's MCP tool
  timeout, a shell `ask` by its shell timeout. Hence bounded waits plus parking.
- **Private-chat topics** (Bot API 9.4) reportedly broke for outbound `message_thread_id` after
  Bot API 10.0 (tdlib/telegram-bot-api#847, closed, fix unconfirmed) — another reason for one chat
  with tags.

## Open questions

- **Where the broker code lives** — this repo is plain Markdown by rule; a separate repo that
  `workspace-sync` installs like codegraph is the leading option.
- CLI face (`notify` · `ask` · `wait`), MCP face, or both — the CLI needs no per-harness config.
- Wait bound before parking, and who resumes a parked run when its answer arrives.
- Broker lifecycle — started by whom, survives which process, how a dead broker degrades to the
  conductor relay.
