# AGENTS.md

Agent-agnostic guide for **this repo**. `README.md` is for people installing the workspace; this
file is for whoever (human or agent) works *on* it.

## Domain

Meta: authoring a Claude Code workspace. The product is **plain Markdown** — instruction files,
skills, templates. No build, no runtime, no tests, no dependencies. Editing prose *is* the work.

## The one rule that shapes everything else

**This repo is not governed by its own content.** Everything under `.claude_TEMPLATE/` is the
artifact being authored — it describes how *other* projects are run, and it does not apply here.
Concretely, in this repo there is:

- no workflow gate, no preflight, no autonomy question — no `/plan`, `/open-sprint`,
  `/dynamic-workflow`, `spec-design`, `maintain-docs`, …
- no sprint, no `artefacts/`, no `docs/` tier system — `backlog/` is the one borrowed convention,
  a plain ticket index for work not being done now, with no board and no sprint above it
- no `project-initialiser` run, no domain, no `CLAUDE.md` scaffold

**Only this root `AGENTS.md` applies**, plus the user's global `~/.claude/CLAUDE.md` rules on
language, MD syntax and commits. Work directly: read the file, discuss, edit, commit.

## Never touch the live workspace

`~/.claude/` is the **installed** copy and is off limits to any work done here. Never `cp`, never
edit a file there to "try something", never repair it by hand.

- Source of truth is `.claude_TEMPLATE/` in this repo. Change it here.
- Syncing into `~/.claude/` happens **only** when the user runs `/workspace-install`, and only the
  user starts it (the skill is `disable-model-invocation`). It is also the repair path.
- Consequence: a change made here is not live until the user syncs *and restarts* Claude Code.
  Say that when handing work over — don't imply an edit took effect.
- `~/.claude/` may legitimately differ from the template: `memory/`, `projects/`, `domains/`,
  `settings.json` are user-owned and never overwritten. A diff there is not automatically a bug.

The live workspace is readable — comparing against it to answer "what would sync change?" is fine.
Writing to it is not.

## Repo layout

```
.claude_TEMPLATE/              ← the product; mirrored into ~/.claude/ by workspace-install
├── CLAUDE.md                  ←   the always-loaded global instruction file — token budget ~2.3k
├── skills/{name}/SKILL.md     ←   one folder per skill; templates/ and agents/ beside it
├── project_TEMPLATE/          ←   scaffold copied into each initialised project
├── domains/domain_TEMPLATE/   ←   domain master scaffold
└── memory/MEMORY.md           ←   global memory seed
backlog/                       ← tickets for this repo's own work (`T-NNN-{slug}.md` + `backlog.md`)
assets/*.svg                   ← README diagrams (session flow, skill/doc map)
README.md                      ← end-user facing: what this is, install, how it fits together
AGENTS.md                      ← this file
```

Not tracked (see `.gitignore`): `.claude/`, `.serena/`, `.tokensave`.

## Authoring conventions

- **Skills are discovered only at `skills/<name>/SKILL.md`** — direct children of `skills/`.
  Grouping subfolders silently break discovery.
- **Every skill needs frontmatter** `name` + `description`; the description is the *only* thing an
  agent picks by, so it must say when to reach for the skill, not what it contains.
  `disable-model-invocation: true` marks user-only skills (currently `workspace-install`).
- **A skill's helper files** (`templates/`, `agents/`, `references/`) live inside its own folder and
  are referenced from `SKILL.md` — they load on demand, which is the whole point.
- **`CLAUDE.md` is always loaded, in every session, forever.** It says *when* something applies and
  *where* the rest lives — never what a skill does (that duplicates the skill's description and
  goes stale). Adding a paragraph there is a permanent cost; default to putting it in a skill.
- **Placeholders** are `{...}` — substituted by a skill or by the user at install time.
- **Write instructions, not explanations.** Skills and `CONTRACT` blocks are read by a model and
  never shipped to a person, so optimise for tokens, not readability: imperative steps, decisions
  with a stated default, no preamble, no restated rationale, no "this is deliberate because…".
  A clause beats a sentence, a sentence beats a paragraph. Justify a rule only where an agent
  would otherwise break it.
- **Compress on write, not after.** Every edit lands in final compressed form and *integrated* —
  a new rule joins the existing list or sentence in the existing voice, never as its own section.
  A reader who can tell which lines are new, because they explain more or sit in a fresh block,
  is looking at a bad edit.
- **One home per fact.** The same rule stated in `CLAUDE.md` and a skill will drift. Reference it.
- **MD syntax** per the global rules: `-` bullets, Unicode trees with aligned `←` comments,
  unpadded pipe tables.
- **README and template stay in sync.** Renaming a skill, changing the kanban semantics or the doc
  tiers means editing `README.md` (and possibly `assets/*.svg`) in the same commit.

## Changing the template

Changes here are cheap to write and expensive to get wrong — they propagate into every project the
user runs. Before editing:

- Ask which of the three layers it belongs to: always-loaded (`CLAUDE.md`), on-demand (`skills/`),
  or per-project (`project_TEMPLATE/`). Pushing a rule down a layer is almost always right.
- Grep the template for the concept being changed — the workflow skills cross-reference each other
  heavily, and a renamed status token or file path usually has 5–10 call sites.
- Structural changes to work items, doc tiers or sprint semantics touch `CLAUDE.md`,
  `project_TEMPLATE/AGENTS.md`, the workflow skills *and* `README.md`. Treat that set as one edit.

The `plugin-dev` and `superpowers:writing-skills` skills are the reference for skill mechanics.

## Version control

Compact commits `<type>: <subject & scope>` in very few words — `feat` `fix` `docs` `refactor`
`chore`. Most work here is `docs:` or `refactor:`. Commit on `main`; the user handles anything else.
No sprint branches in this repo.

## Verification

There is nothing to run. "Done" means: the prose says what it means, cross-references resolve,
frontmatter is well-formed, and `README.md` still matches the template. Check by reading, and by
grepping for the terms touched.
