---
name: domain-initialiser
description: "Use to create or rebuild a domain master plugin under ~/.claude/domains/{x}-domain/ — e.g. when project-initialiser needs a domain that doesn't exist yet, or to refresh an existing one. Explicit-invoke."
disable-model-invocation: true
---

# Domain Initialiser

Turn a recipe into a self-contained **master domain plugin** under
`~/.claude/domains/{x}-domain/`. The workspace ships **recipes, not plugins** — this
skill builds the plugin. The master stays **inert** here (`domains/` is not a skills
dir); it activates only when `project-initialiser` copies it into a repo's
`.claude/skills/`.

## 1. Recipe — skip if `Domain-Recipe.md` already exists

Research, then write the recipe:

- **Websearch** the domain's official docs and its unit + UI/integration test frameworks.
- **Search the Claude marketplace** (`/plugin`) for reusable skills, MCP servers and agents.
- Copy `domains/domain_TEMPLATE/` → `domains/{x}-domain/`, rename the recipe to
  `Domain-Recipe.md`, and fill every section. The recipe is the single source of truth.

## 2. Validate — pause

Present the recipe to the user and get approval **before building anything**. Stop
here until they confirm.

## 3. Build the plugin from the recipe

Vendor everything **into the plugin folder** so the master is self-contained — do not
install domain skills/MCPs globally.

1. `.claude-plugin/plugin.json` — set `name` to `{x}-domain`.
2. **Skills** — for each in the recipe: marketplace-sourced → copy its folder into
   `skills/`; custom → author `skills/{name}/SKILL.md` (use superpowers' skill-creator),
   citing the recipe's doc links.
3. **MCP servers** — add each to `.mcp.json` (`mcpServers`) and install the package it
   needs. Approval stays project-scoped — handled later via the repo's `.mcp.json`.
4. **Agents** — add `agents/{name}.md`; set `model:` per task (Sonnet for runners).
5. `skills/`, `agents/`, and `.mcp.json` are auto-discovered — no manifest wiring needed.

## 4. Verify

- `plugin.json` and `.mcp.json` parse as JSON; every `SKILL.md` has valid frontmatter.
- The test-framework install command is recorded in the recipe (project-initialiser runs it).
- Real activation is confirmed later, when project-initialiser copies the master into a repo.
