# Token savers — 2026-09-23

The token-saver slice of [`repo-survey.md`](repo-survey.md), in depth. Settles headroom:
**not reinstated.** No compression layer is worth wiring in on vendor numbers; the one cheap,
independently backed win is output-side, and anything heavier waits for a measurement.

## Evidence

Star counts in this niche are unusable — 2026 repos at 24k–120k stars with a 0.2–0.4% watcher
ratio (1–5% is normal). Only three independent measurements exist:

- **rtk, JetBrains paired A/B** (86 tasks, 425 billed trials): median **+7.6% cost** at low
  reasoning effort, flat at high. Its hook sees only Bash calls; Read/Grep/Glob bypass it, and its
  dashboard compares against raw output rather than the harness's own truncation.
- **caveman skill, JetBrains**: −8.5% output tokens, quality flat (p=0.82).
- **Adobe, arXiv 2606.24083**: output-side compression saves 1.4–3× realised cost; input-side
  prompt compression is counterproductive — the class headroom's proxy belongs to.

## Survey

| Tool | Acts on | Can mutate the prompt | Evidence | License | Verdict |
| --- | --- | --- | --- | --- | --- |
| headroom | every API request (proxy/wrap), or what is passed to its MCP | yes | vendor walked 60–95% back to 15–20% for coding agents; unmeasured | Apache-2.0 | skip |
| rtk | Bash tool output, via PreToolUse hook | no | independently negative | Apache-2.0 | skip |
| caveman skill | the agent's own prose | no | independently positive | MIT | trial |
| caveman proxy | tool output | yes | own benchmark only | BSL-1.1 | skip |
| context-mode | tool calls: runs them sandboxed, indexes output in SQLite FTS5, returns the relevant slice | no | vendor only | ELv2 | first candidate if a layer is needed |
| codegraph | code exploration | no | transparent own benchmark: −62% tokens, −44% cost | MIT | required already |
| graphify | code + docs/PDF/media graph | no | own benchmark | Apache-2.0 | watch — code half duplicates codegraph |
| hindsight | cross-session memory (Postgres/pgvector service) | no | claims external reproduction, unverified | MIT | skip — competes with Markdown memory |

Headroom specifics that decided it: telemetry on by default (`HEADROOM_BEACON=off`), `wrap`
installs Serena MCP at user scope until `unwrap`, daily update check; the one independent review
warns prompt-cache invalidation can cancel token savings on the bill. codegraph's own trade-off:
its verbatim payloads leave more tokens resident late in a long session (67k vs 18k on its VS Code
case) — the price of fewer tool calls.

## Measuring a candidate

Billed cost per completed task, never a tool's own dashboard: 2–3 real tasks (e.g. a
`dynamic-workflow` implement stage heavy on tool output), 3 runs each with and without, compare the
harness's reported cost and tokens per run. codegraph already removes much of the large-output
surface, so the margin left for any layer is smaller than vendor numbers assume.
