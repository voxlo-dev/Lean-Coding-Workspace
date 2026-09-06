# Connectors — the three homes of a model's settings

A dispatch that runs but behaves wrong is almost always the wrong home. There are three, and only
the first is the workspace's:

| Home | Holds | Lives in |
| --- | --- | --- |
| The table | which model a role gets — an ID, nothing more | `~/.agents/dispatch.json` |
| The connector | how that model **loads** — context, quant, sampling, GPU placement | the model server's own config |
| The harness model entry | what **every request** carries — max tokens, reasoning effort | the target harness's config |

The third beats the second: an explicit request field always wins over a server-side default. So a
role switching models never means a table entry carrying launch flags, and never means one server
per role.

## Recipe A — any OpenAI-compatible endpoint

llama.cpp, Unsloth Studio, LM Studio, vLLM, a RunPod pod: one provider block differing only in
`baseURL` and key. In OpenCode's config (`~/.config/opencode/opencode.json`):

```json
{
  "provider": {
    "unsloth": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Unsloth Studio",
      "options": { "baseURL": "http://localhost:1234/v1", "apiKey": "not-needed" },
      "models": {
        "qwen3.5-9b-mtp": { "name": "Qwen3.5-9B-MTP", "limit": { "context": 96000, "output": 8000 } }
      }
    }
  }
}
```

The table then references `unsloth/qwen3.5-9b-mtp`. A remote vendor API is the same block with the
vendor's `baseURL` and a real key; use `{env:VAR}` rather than pasting the key.

**The model entry is where per-request parameters live, and it is silently unforgiving.**
`limit.output` becomes the request's `max_tokens`. Reasoning effort reaches the model **only** as
`options.reasoningEffort` — camelCase, translated to `reasoning_effort` in the body. A model-level
`effort` key, snake_case inside `options`, and the `--variant` flag are all dropped without a word;
the schema declares `additionalProperties: false` yet an unknown key loads without a warning. Only a
proxy capture of the real request body settles a dispute here.

## Recipe B — an aggregator

OpenRouter and the other built-in providers need no block: `opencode auth login`, pick the provider,
paste the key. The table then names `openrouter/<model>`.

**The same model costs different tokens by route.** Identical prompts measured 20.7k input direct
from the vendor and 36.1k for the same model through an aggregator, and only the aggregated route
reported reasoning tokens. On a paid key that is a straight cost difference for identical work —
compare routes before pinning one in the table.

## One endpoint, many models

A server with on-demand loading (Unsloth Studio's `openai_auto_switch`, off by default) swaps the
model in when a request names one that is not loaded. Loads measured 17-51 s and scale with file
size, so a role changing model costs well under a minute — cheap enough that a per-role table needs
**one** endpoint, not one server per role.

**Per-model overrides are keyed by model *and quant*.** An override written without the quant suffix
becomes a catch-all across every quant of that model; one written for a quant that is not the one
being loaded does nothing. Both fail silently.

## Choosing a model for a role

Measured on one identical two-tool probe, so this ranks **fitness for dispatch**, not model quality:

- **Active parameters beat total size locally.** A 35B MoE with ~3B active ran 62 s; a 27B dense
  model ran 176 s on the same hardware. For local dispatch targets, MoE architecture matters more
  than parameter count.
- **Free-tier latency tracks the queue, not the model.** Hosted runs from 29 s to 2065 s for the
  same work, with the throughput-tuned model slower than its heavyweight sibling. Anything past
  ~500 s is unusable for thin dispatch, where one unit costs three to four calls.
- **The interface itself is model-independent.** From a 9B GGUF to a 2.8T hosted model, only the
  model string changed — same agent, same flags, same status line, same enforced wall. The wall is
  enforced at the tool layer, so it holds regardless of how strong or cooperative the model is.

## The one way the wall silently voids

Path denies are **last-match-wins, not deny-wins**. A catch-all `"*": allow` listed *after* the deny
globs re-opens every one of them, with no warning:

```yaml
permission:
  read:
    "*": allow          # broadest first
    "**/tests/**": deny # denies after it
```

A voided wall looks exactly like a passing run except for what the status line reports. Verify a new
agent's denies with one probe dispatch before trusting it.
