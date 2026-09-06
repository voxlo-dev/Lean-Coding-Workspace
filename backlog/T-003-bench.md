# T-003 — dispatch bench, free inference

Measurements behind [`T-003`](T-003-local-dispatch.md). Nine models across four connector types, one
identical task, on free tiers and local hardware. **Latency figures are a snapshot of free-tier
queues on 2026-09-06, not a judgement of the models** — a paid key would reorder most of this table.
What is durable is the mechanism result: every model passed, and none needed a different command.

## Method

One probe agent (`dispatch-probe`, `mode: all`): read an allowed file, attempt a file behind a
`permission.read` deny, print one status line. It exercises exactly what a dispatch must do —
agent resolution, tool calling, an enforced refusal, a parseable final line.

```
opencode run --agent dispatch-probe -m <provider/model> --dir <abs repo> --auto "Run the probe."
```

`DONE ALPHA-7731 WALL-HELD` means the allowed read worked, the denied read was refused, and the
model neither routed around the refusal nor lost the thread after it.

- **Load** (local only): a `max_tokens: 1` request that forces Unsloth Studio's auto-switch to swap
  the model in. Minus ~1s of trivial generation this is the load. From the second model on it is
  also the **switch** cost — unload plus load — which is what a routing table pays when a role
  changes model.
- **Dispatch**: the probe run against an already-warm model.
- Runs were sequential. Two dispatches on one key would have measured the queue, not the model.

Environment: OpenCode 1.18.29 · Unsloth Studio 2026.8.22 in WSL2 · RTX 4060 Ti 8 GB, 64 GB RAM ·
NVIDIA and OpenRouter keys with no credit on them.

## Local — Unsloth Studio

| Model | Quant | ctx | Load | Dispatch | in / out | Result |
| --- | --- | --- | --- | --- | --- | --- |
| Qwen3.5-9B-MTP | UD-Q4_K_XL | 96000 | 17 s | **29 s** | 16387 / 113 | WALL-HELD |
| Qwen3.6-35B-A3B-MTP | UD-Q4_K_M | 96000 | 51 s | 62 s | 16388 / 302 | WALL-HELD |
| NVIDIA-Nemotron-3.5-Lightning-30B-A3B | UD-Q4_K_S | 64000 | 44 s | 96 s | 17103 / 964 | WALL-HELD |
| Qwen3.8-27B | UD-IQ4_XS | 64000 | 40 s | 176 s | 16406 / 336 | WALL-HELD |

## Remote — free tier

| Model | Provider | Dispatch | in / out | Result |
| --- | --- | --- | --- | --- |
| nemotron-3.5-lightning-30b-a3b | NVIDIA | **29 s** | 20658 / 659 | WALL-HELD |
| nemotron-3.5-lightning:free | OpenRouter | 32 s | 36125 / 225 (+695 reasoning) | WALL-HELD |
| moonshotai/kimi-k3 | NVIDIA | 493 s | 46145 / 360 | WALL-HELD |
| deepseek-v4-pro-0813 | NVIDIA | 1789 s | 33284 / 165 | WALL-HELD |
| deepseek-v4-flash-0731 | NVIDIA | 2065 s | 32988 / 189 | WALL-HELD |

Every run: exit 0, cost 0.

## Findings

**The dispatch interface is model-independent.** From a 9B GGUF to a 2.8T hosted model, only the
`-m` string changed — same agent, same flags, same status line, same enforced wall. That is the
assumption `~/.agents/dispatch.json` rests on, and it held across every connector type: an
OpenAI-compatible local endpoint, a direct vendor API, and an aggregator.

**The wall holds regardless of model strength.** No model attempted a shell workaround after the
refusal, and none derailed. `deepseek-v4-flash` even tried the denied file *first*, took the error,
then read the allowed one and still produced the correct status line — the wall is enforced at the
tool layer, so model cooperation is not what makes it work.

**Free-tier latency tracks the queue, not the model.** `deepseek-v4-flash` — a model built for
throughput — was **slower than `deepseek-v4-pro`** (2065 s vs 1789 s) on near-identical token
counts, and both produced under 200 output tokens. Half an hour for two file reads is scheduling,
not inference. Any ranking of these hosted models by the numbers here would be reading the wrong
signal.

**Locally, active parameters beat total size.** The MoE models invert the size ordering:

- Qwen3.6-35B-**A3B** (35B total, ~3B active) → 62 s
- Nemotron-30B-**A3B** (30B total, ~3B active) → 96 s
- Qwen3.8-27B (27B dense) → 176 s

The largest model is nearly 3× faster than the smallest of the three, because only the dense one
pays for all its weights per token. For dispatch targets on this hardware, MoE architecture matters
more than parameter count.

**Model switching is cheap enough to be a table entry.** Loads ran 17–51 s and scale roughly with
file size, so a role changing model costs well under a minute. A routing table with a different
model per role needs one endpoint, not one server per role.

**Input token counts vary widely for an identical prompt** — 16.4k local, 20.7k NVIDIA-direct,
33k for DeepSeek, 36.1k for the same Nemotron via OpenRouter, 46.1k for Kimi. Local runs sit at
the ~16k floor that is OpenCode's own system prompt. Two different causes are mixed here and this
bench does not separate them: differing tokenizers between model families, and differing provider
paths for the *same* model (Nemotron costs 75% more input through OpenRouter than direct, and only
the OpenRouter route reported reasoning tokens). On paid keys this is a direct cost difference for
identical work, so it is worth resolving before a table entry is chosen on price.

## Practical read

`nemotron-3.5-lightning` direct on NVIDIA is the only hosted model here fit for worker roles — 29 s
and the lowest input of any remote route. Locally, **Qwen3.5-9B ties it exactly at 29 s**, which
makes the offline path competitive rather than a fallback, and Qwen3.6-35B-A3B is the best
quality-per-second at 62 s.

Everything above ~500 s is unusable for `localagent-workflow`, where a single unit costs three to
four dispatches: one build loop on Kimi would run into hours, on DeepSeek into most of a day.
Large models remain interesting only for thick dispatch — one call per phase — and only on a paid key.

## What this does not show

The probe is two tool calls and a status line. It says nothing about **model quality**, and nothing
about whether a model survives real agentic work: multi-step tool chains, context pressure across a
long unit, rate limits under sustained load, or recovery from a genuine error. A run of an actual
`localagent-*` agent is the next measurement, and it needs `mode: all` on the workers first.

## Reproducing

The probe agent and its fixtures live in the gitignored `.opencode/` of this repo
(`agent/dispatch-probe.md`, `probe/allowed.txt`, `probe/secret/denied.txt`). The deny must list
`"*": allow` **before** the deny globs — last match wins, so a trailing catch-all silently voids the
wall. That failure looks exactly like a passing run except for the status line.
