# Coding-agent repo survey — 2026-09-23

41 repos around coding agents, judged for this workspace: agent-agnostic, plain skill folders,
nothing always-loaded that a skill could carry. Verdicts: **in use** (part of the workspace) ·
**catalog** (row in `.agents/skills/workspace-sync/references/bundles.md`) · **trial** · **watch** ·
**skip**. Token savers in depth: [`token-savers.md`](token-savers.md).

**Stars carry no signal here.** 2026 repos at 50k–250k stars show a 0.2–0.4% watcher ratio
(1–5% is normal); every verdict rests on mechanism, license and independent evidence instead.
Several repos moved owner, and same-name copies are appearing — check the owner before installing.

| Repo | Category | What | Integration | Standing cost & risk | License | Verdict | Note |
| --- | --- | --- | --- | --- | --- | --- | --- |
| obra/superpowers | workflow | brainstorm → plan → subagent TDD | skill folders; plugin with SessionStart hook | plugin injects a bootstrap every session; bare skills none | MIT | catalog | without `using-superpowers` |
| mattpocock/skills | workflow | small composable skills: grill → spec → tickets | skill folders grouped by category | setup writes an `AGENTS.md` section and its own issue convention | MIT | catalog | flattened |
| addyosmani/agent-skills | workflow | full lifecycle with web, perf, security checklists | skill folders + `references/` | none | MIT | catalog | `../../references/` paths rewritten on install |
| Fission-AI/OpenSpec | workflow | change-based specs: propose → apply → archive | skill folders + npm CLI | none; keeps `openspec/` per project | MIT | catalog | |
| bmad-code-org/BMAD-METHOD | workflow | agile method with personas: PRD, architecture, epics | skill folders + `uv` + per-project setup | ~32 long descriptions | MIT | catalog | heaviest entry |
| github/spec-kit | workflow | constitution-based spec development | Python CLI generates everything | `.specify/` constitution competes with `AGENTS.md` | MIT | skip | no copyable skill folders |
| multica-ai/andrej-karpathy-skills | skills | four behaviour rules against common LLM coding mistakes | one skill | none | MIT | catalog | moved from forrestchang |
| Nutlope/hallmark | skills | UI generation off the template look: 21 themes, 57 checks | one skill + ~80 on-demand references | none | MIT | catalog | pairs with `ui-design` |
| anthropics/skills | skills | webapp-testing, mcp-builder, skill-creator | skill folders | none | Apache-2.0 per skill | catalog | document skills are proprietary |
| anthropics/claude-plugins-official | plugins | official marketplace: frontend-design, feature-dev | plugins | 6 plugins ship hooks | Apache-2.0 | catalog | those two only |
| affaan-m/ECC | workflow | everything at once: 290+ skills, 68 agents, rules | npm installer | hooks on six lifecycle events, always-loaded rules | MIT | skip | own `e2e-runner` collides |
| gmickel/flow-next | workflow | spec workflow, fresh workers, cross-model review | plugin/CLI | unverified | MIT | trial | read for patterns |
| Gentleman-Programming/gentle-ai | workflow | agent-agnostic config layer: memory, skills, MCP | CLI/skill | unverified | MIT | trial | same philosophy — read |
| maxritter/pilot-shell | workflow | spec + TDD + memory + quality gates in one package | plugin/CLI | unverified | unclear | watch | check license |
| shinpr/claude-code-workflows | workflow | keeps exploration bounded to the approved outcome | skill/plugin | unverified | MIT | watch | |
| wshobson/agents | agents | 100+ specialist agents, multi-harness | plugin/agents | high if installed whole | MIT | watch | source for single agents |
| msitarzewski/agency-agents | agents | ~295 persona agents | agent files + per-harness converter | none | MIT | catalog | `engineering/`, `testing/` |
| headroomlabs-ai/headroom | tokens | compression proxy for tool output, logs, history | proxy, wrap, MCP, SDK | proxy rewrites all traffic; telemetry on | Apache-2.0 | skip | [token-savers](token-savers.md) |
| colbymchenry/codegraph | tokens | code knowledge graph replacing grep/read loops | MCP | one tool schema; more context resident late in long sessions | MIT | in use | required capability |
| upstash/context7 | tokens | current library docs | MCP/plugin | tool schemas | MIT | in use | required capability |
| Graphify-Labs/graphify | tokens | graph over code plus docs, PDFs, media | skill/MCP | non-code ingest calls an LLM | Apache-2.0 | watch | code half duplicates codegraph |
| rtk-ai/rtk | tokens | filters shell output via PreToolUse hook | hook + Rust binary | hook on every Bash call | Apache-2.0 | skip | independently +7.6% cost |
| JuliusBrussee/caveman | tokens | terse-output skill; tool-output proxy | skill (MIT) + proxy (BSL-1.1) | skill none; proxy rewrites traffic | MIT / BSL-1.1 | trial | skill only |
| mksglu/context-mode | tokens | sandboxes big tool output, returns the relevant slice | MCP + hooks | 6 hook types; no telemetry | ELv2 | watch | first candidate if a layer is needed |
| vectorize-io/hindsight | memory | agent memory on Postgres/pgvector | server + SDKs | standing database service | MIT | skip | competes with Markdown memory |
| thedotmack/claude-mem | memory | automatic session capture with vector search | plugin: 5 hooks, daemon, SQLite + Chroma | high; optional paid cloud | Apache-2.0 | skip | inverts curated Markdown memory |
| vercel-labs/agent-browser | browser | drives Chrome via CDP and accessibility refs; own MCP | Rust CLI + MCP | small (`core` profile); native Windows | Apache-2.0 | trial | e2e driver for web projects |
| ChromeDevTools/chrome-devtools-mcp | browser | official Chrome DevTools as MCP tools | MCP | tool schemas | Apache-2.0 | trial | alternative to agent-browser |
| github/github-mcp-server | review | official GitHub MCP: issues, PRs, reviews, code search | MCP | tool schemas; needs a token | MIT | in use | inside the optional `github` plugin |
| Nikita-Filonov/ai-review | review | CI code review, local Ollama supported | CLI/CI | none in the harness | Apache-2.0 | watch | |
| backnotprop/plannotator | review | visual annotation of plans and diffs | plugin/app | unverified | Apache-2.0 | watch | |
| ruvnet/ruflo | orchestration | swarm orchestration: 98 agents, 323 MCP tools | plugin/CLI | writes into `CLAUDE.md`, 27 hooks, daemons | MIT | skip | |
| NousResearch/hermes-agent | orchestration | standalone agent with gateway, cron, self-learning | own runtime | own service; optional external profiling (Honcho) | MIT | skip | replaces rather than augments |
| alvinunreal/oh-my-opencode-slim | orchestration | lean OpenCode multi-agent suite mixing models | OpenCode plugin | unverified | MIT | watch | relevant to dispatch |
| humanlayer/humanlayer | orchestration | human-in-the-loop approval layer | SDK/CLI | unverified | unclear | watch | check license |
| getpaseo/paseo | orchestration | drive several coding agents from desktop and phone | app | unverified | unclear | watch | check license |
| openchamber/openchamber | orchestration | development environment built on OpenCode | app | unverified | MIT | watch | |
| morganlinton/Albatross | orchestration | terminal agent routing across local and cloud models | own TUI | unverified | MIT | watch | |
| ComposioHQ/awesome-claude-skills | lists | link list plus 832 Composio skills | — | — | none | skip | useful items come via anthropics/skills |
| shanraisshan/claude-code-best-practice | lists | guide with a workflow overview | — | — | MIT | skip | source of leads |
| Shubhamsaboo/awesome-llm-apps | lists | LLM app tutorials | — | — | Apache-2.0 | skip | |

Unverified: standing cost of flow-next, gentle-ai, pilot-shell, claude-code-workflows, humanlayer,
paseo, plannotator · claudemarketplaces.com, unreachable during the survey.
