# T-009 — Trial context-mode

- **Summary:** measure whether context-mode's sandboxed tool output cuts billed cost without hurting results, then decide on a catalog row
- **Category:** chore
- **Importance:** low
- **Effort:** S
- **Depends on:** none

## Why

Tool output, not the agent's prose, is where a coding run spends its input tokens; context-mode
attacks exactly that — runs tool calls sandboxed, indexes the output in SQLite FTS5, returns only
the relevant slice. It is the first candidate in [`token-savers.md`](../docs/token-savers.md) should
a layer be needed, but its evidence is vendor-only.

## What

- Install https://github.com/mksglu/context-mode (MCP + hooks, no telemetry); check that ELv2
  permits the use and which of its 6 hook types each harness supports.
- Run the measurement in [`token-savers.md`](../docs/token-savers.md#measuring-a-candidate) on
  tool-output-heavy tasks, and watch for a slice that drops what the agent needed.
- Worth it → a row in `.agents/skills/workspace-sync/references/bundles.md`; not → its verdict in
  `docs/token-savers.md`.
