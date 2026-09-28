<!-- CONTRACT — filled by harness-onboard; on first fill strip this block, leaving:
     Template: `.agents/skills/harness-onboard/templates/MANIFEST.md`
- Every row stays. Value is the verified answer · `gap` = the harness lacks it · `unknown` = nothing answered yet. Never a guess.
- Verified = version · date · how (lever, probe, string dump, docs). A version-bound fact expires with its version.
- Gotchas and levers: one line each, only what changes what an install, a probe or a run does.
-->

# {Harness} — adapter manifest

Binary: {how it resolves} · {version}

## Install

| Fact | Value | Verified |
| --- | --- | --- |
| Home | | |
| Global instructions | {file} · imports: {yes · no} | |
| Instruction budget | {limit and truncation behaviour} | |
| Skills | {reads `~/.agents/skills` natively · else the link mechanism} | |
| Agents | {dir} · {format} · ID from {…} · convert: {transform · none} | |
| Dispatch | {tool} · proof: {the child's own record} | |
| Memory lever | {import · instructions list · none} | |
| Project memory | {native path · gap} | |
| MCP | {config file and entry shape} | |
| Plugins | {mechanism · gap} | |
| Config reload | {hot · restart} | |
| Custom provider | {how to point it at a local endpoint} | |

## Project

| Fact | Value | Verified |
| --- | --- | --- |
| Project config | {file} · trust: {model} | |
| Skill roots | | |
| Agents | | |
| MCP | | |
| Instruction shim | {needed, and what it imports · none} | |

## Verification levers

- {command} — {what it shows, without a model call where possible}

## Gotchas

- {fact that breaks an install or a run silently}

## Proven

{date · model · what an end-to-end run established, per capability}
