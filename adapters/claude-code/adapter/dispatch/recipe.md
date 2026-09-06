# Dispatch recipe — Claude Code → OpenCode

Claude Code dispatches Anthropic models only and restricts subagents by tool name, never by path.
Both gaps are OpenCode's to fill, so a dispatch **shells out**.

## The command

```
opencode run --agent <agent-name> -m <provider/model> --dir <absolute repo path> --auto "<brief>"
```

- **Launcher** — bare `opencode` is a POSIX-only name. On Windows the npm shim is `opencode.cmd`
  (PowerShell: `opencode.ps1`); from the Bash tool on Windows call `opencode.cmd`. Getting this
  wrong reads exactly like a dead connector.
- `--dir` must be **absolute**. The dispatched agent knows nothing about your cwd.
- `--auto` runs unattended. It does not cover an MCP server a project's `opencode.json` registers —
  those add startup cost and can prompt; a repo with heavy MCP config is worth one trial dispatch.
- The brief is one shell argument. Multi-line: a heredoc into `--auto "$(cat …)"`, never a
  hand-quoted blob.

Capture stdout **and** stderr to the transcript file; the status line is stdout's last line.

## Reading the result

| Signal | Means |
| --- | --- |
| exit 0, last line a status token | the dispatch ran — report that line + the transcript path |
| exit 0, no status token | the agent lost the contract → `BLOCKED` |
| exit 1, `Error: Cannot connect to API` | dead connector → `BLOCKED`, never a fallback model |
| header names an agent you did not ask for | you passed a subagent-only name; it ran unwalled → treat as failed |

## Permission noise

Every dispatch is a Bash call, so the default permission mode prompts once per dispatch — which
makes a multi-step run unusable. Fix it deliberately rather than by loosening the mode: add
`"Bash(opencode run:*)"` to `permissions.allow` in the project's `.claude/settings.json`. That
allows dispatch and nothing else. It is the user's call; ask before writing it.

## Watching a live run

Every dispatch appears in OpenCode's shared session store within seconds of starting, with no flag.
`opencode attach <url>` (the TUI) reads it. The shipped web UI in 1.18.x does not — its bundle
requests a route the server does not implement, so the project list stays empty. Human-facing
either way; never wait on it.
