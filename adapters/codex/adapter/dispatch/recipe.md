# Dispatch recipe — Codex → OpenCode

Codex reaches its own vendor's models and offers no per-agent path scoping, so a dispatch **shells
out** to OpenCode.

## The command

```
opencode run --agent <agent-name> -m <provider/model> --dir <absolute repo path> --auto "<brief>"
```

- **Launcher** — bare `opencode` is a POSIX-only name; on Windows call `opencode.cmd`
  (PowerShell: `opencode.ps1`).
- `--dir` must be **absolute**; the dispatched agent knows nothing about your cwd.
- `--auto` runs unattended. It does not cover an MCP server a project's `opencode.json` registers.
- Codex's shell tool is sandboxed and network-restricted by default. A dispatch needs to reach the
  connector's endpoint — a local server on `localhost`, or the open internet for a hosted one — so
  the dispatch call must run with network access enabled, or every dispatch reports a dead
  connector. Verify with one probe before trusting a run.

Capture stdout **and** stderr to the transcript file; the status line is stdout's last line.

## Reading the result

| Signal | Means |
| --- | --- |
| exit 0, last line a status token | the dispatch ran — report that line + the transcript path |
| exit 0, no status token | the agent lost the contract → `BLOCKED` |
| exit 1, `Error: Cannot connect to API` | dead connector → `BLOCKED`, never a fallback model |
| header names an agent you did not ask for | you passed a subagent-only name; it ran unwalled → treat as failed |

## Watching a live run

`opencode attach <url>` (the TUI) reads OpenCode's shared session store, where every dispatch
appears within seconds of starting. Human-facing; never wait on it.
