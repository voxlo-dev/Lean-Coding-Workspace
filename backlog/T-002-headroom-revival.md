# T-002 — Headroom revival

- **Summary:** Headroom revival — decide whether to reinstate headroom as a compression proxy, and in which integration mode
- **Category:** decision
- **Importance:** low
- **Effort:** S
- **Depends on:** none

## Why

Headroom was dropped as a mandatory plugin because `headroom wrap claude` was believed to be the
only entry point — console only, no Claude UI, plus an assumed performance cost. Two of those
premises need revisiting, and one belief about the tool was simply wrong.

**It is not a context meter.** Headroom is a compression proxy: it shrinks tool outputs, logs, RAG
chunks, files and history before requests reach the model. Vendor claims 60–95% fewer tokens on
JSON payloads and 15–20% for coding agents — unverified. It therefore does nothing for the
"how full is my context right now" gap that [[T-001]] runs into.

**Wrapping is one of five modes.** `headroom proxy --port 8787` is a standalone drop-in gateway
that needs no agent-side changes and leaves the UI intact; `headroom mcp serve` exposes
`headroom_compress`, `headroom_retrieve`, `headroom_stats`; there are also SDK middleware, direct
library calls and framework adapters.

Current local state: nothing installed, no config. The stale `mcpServers.headroom` stdio entry
(`headroom mcp serve`) that produced `-32000 Connection closed` at session start is gone from
`~/.claude.json`, so reviving headroom means a fresh install plus a fresh entry.

## What

Settle whether headroom comes back, and if so in which mode.

Forces:

- The claimed 15–20% saving on coding agents is the whole case for it, and it is unmeasured. Any
  decision to adopt should follow an actual before/after measurement on a real run, not the
  vendor number.
- Latency and CPU overhead are undocumented — measure, don't assume.
- Proxy mode routes all traffic through a third-party process that both *reads* and *mutates*
  prompts. Acceptable for a meta repo like this one; a separate judgement for client code.
- MCP mode avoids the traffic-interception concern entirely but only compresses what is explicitly
  passed to its tools, so the payoff is much smaller.

Installation, if it goes ahead: `uv tool install --python 3.13 "headroom-ai[all]"` (also pip;
npm ships the TypeScript SDK only).
