# T-008 — Project a domain instead of bundling it per harness

- **Summary:** drop the per-harness plugin manifests and make a domain a neutral capability bundle that `project-initialiser` projects onto the paths each harness already scans — which also buys several domains per project and non-coding domains
- **Category:** refactor
- **Importance:** high
- **Effort:** L
- **Depends on:** none — but `T-006`'s capability manifest inherits the projection table below, so land this first

## Why

The current master carries a `.claude-plugin/plugin.json` and a `.codex-plugin/plugin.json` and is
copied into `{repo}/.agents/skills/{x}-domain/`. That does nothing in at least two of three
harnesses: Claude Code loads plugins only through `extraKnownMarketplaces` + `enabledPlugins`, never
from a folder it happens to find, and OpenCode has no comparable system at all (its `plugin` key
takes JS modules). Worse, the copy nests the domain's skills at
`.agents/skills/{x}-domain/skills/{name}/SKILL.md`, and every harness discovers a skill only as a
**direct** child of a skills root. A domain installed today ships nothing.

Two limits fall out of the same bundling choice. The recipe is a code stack's shape — test
frameworks, release tracks, LSP — so a writing or research domain has to fill mandatory fields with
`none`. And one folder per repo means one domain per repo, when a frontend and a backend in one
tree want two.

## What

### 1. The master goes neutral

```
~/.agents/domains/{x}/
├── Domain-Recipe.md      ← identity, doc sources, conventions, engineering seed (optional)
├── DOMAIN-MEMORY.md
├── skills/{name}/SKILL.md
├── agents/{name}.md
└── mcp.json              ← {name: {command, args}} or {name: {url}}
```

`.claude-plugin/`, `.codex-plugin/` and `.mcp.json` go, and with them `{home}/adapter/domain/` as a
whole folder. `mcp.json` replaces them: the three target formats are the same two fields in three
syntaxes, so it is a translation, not a manifest. The `-domain` suffix drops from the folder name —
under `domains/` it says nothing.

### 2. `project-initialiser` projects, per domain and per selected harness

| Unit | Target | Mode `link` (default) | Mode `copy` |
| --- | --- | --- | --- |
| skill folder | `.agents/skills/{name}` **and** `.claude/skills/{name}` | junction on the master, gitignored | real copy, tracked |
| agent | `.claude/agents/` · `.opencode/agent/` · `.codex/agents/*.toml` | copy, TOML transform for Codex | same, tracked |
| MCP | `.mcp.json` · `opencode.json` · `.codex/config.toml` | merge | merge |
| `DOMAIN-MEMORY.md` | import / `instructions` entry | **always the master**, never a copy | always the master |

Both skill links point straight at the master, never at each other — no junction chain. The mode is
`project-initialiser`'s question per project: `link` keeps one home and propagates master edits
instantly, `copy` gives a clone the domain without the workspace.

### 3. Several domains are a union

Peers, no precedence. Colliding skill names are surfaced and asked about, never auto-prefixed — a
prefix falsifies the description an agent picks by. A colliding MCP server name is an error.
Domain memory is one import line per domain.

`.agents/domains.md`, **tracked**, is the ledger: which domain, which mode, what was placed where.
In `link` mode the links themselves are gitignored, so this file is what lets a fresh clone
reconstruct the projection exactly. The project's `AGENTS.md` names the domains in prose and points
here.

### 4. The engineering seed loses its authority

Test frameworks, release rules and build commands stay in the recipe but become **seed**:
`project-initialiser` fills `docs/release.md` and `docs/dev.md` from them once, and the project is
the authority afterwards. This is what makes several domains composable at all, and a non-coding
domain simply omits the section instead of writing `none` into it. A domain contributes skills,
agents, MCP, memory and doc sources — nothing else, and no workflow skill changes behaviour because
a domain is installed.

## Verified 2026-09-17 — the facts this design rests on

Docs plus string dumps of the installed builds; the junction probe was run in this repo and removed.

| Harness | Skill roots (project) | Agents | MCP |
| --- | --- | --- | --- |
| Codex 0.153 | `.agents/skills/`, `.codex/skills/`, cwd → repo root | `.codex/agents/*.toml` | `.codex/config.toml`, trusted only |
| OpenCode 1.18 | `.agents/skills/`, `.claude/skills/`, `.opencode/skills/`, cwd upwards | `.opencode/agent/*.md`, singular, confirmed on disk | `opencode.json` `mcp` |
| Claude Code | `.claude/skills/` only | `.claude/agents/*.md`, recursive, ID from `name:` | `.mcp.json` + `enableAllProjectMcpServers` |

- `grep -a '\.agents' claude.exe` returns nothing: Claude Code reads no `.agents` path at any scope,
  so the per-skill junction is the only bridge and stays one.
- **Junctions resolve in all three.** A throwaway skill in the scratchpad, junctioned into this
  repo's `.agents/skills/`: `opencode debug skill` lists it under the *link* path,
  `codex debug prompt-input` carries it in the prompt under skill root `r8`. Claude Code was already
  proven.
- OpenCode's `skills.paths` would point at a master without any link, but Codex and Claude Code have
  no equivalent — one of three is a hole, which is why links carry this.

## Call sites

One edit, ~13 files. The `{x}-domain` → `{x}` rename runs through most of them.

- `workspace_TEMPLATE/AGENTS.md` — Domains section, the `{home}/adapter/` inventory, the Memory
  domain row (in `link` mode there is no repo copy to warn about)
- `skills/domain-initialiser` — manifests step becomes `mcp.json`; recipe sections turn optional
- `skills/project-initialiser` — detect domain**s**; step 6 becomes the projection, the mode
  question and the ledger; the test framework comes from the seed into `docs/`
- `skills/release` — the runbook is the authority, seeded at init, no longer "domain first"
- `skills/e2e` — the driver choice reads `docs/dev.md`, not the recipe
- `skills/maintain-memory` — domain scope with several masters
- `domains/domain_TEMPLATE/Domain-Recipe.md` — engineering block optional, scope field added
- `project_TEMPLATE/AGENTS.md`, `project_TEMPLATE/docs/release.md` — Domain → Domains, inheritance
  wording
- `.agents/skills/workspace-sync` — the `domain/` row leaves the adapter table
- `adapters/*/adapter/domain/` — deleted
- `README.md` and `assets/*.svg` — the Domains section describes a projection, not a plugin
