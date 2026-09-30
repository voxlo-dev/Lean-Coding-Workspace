# Contributing

Thanks for helping. This repo is plain Markdown: editing prose *is* the work. Read
[`AGENTS.md`](AGENTS.md) first — it holds the authoring conventions every change is judged by.

## What gets accepted

- Fixes: contradictions, broken cross-references, wrong or outdated harness facts (with evidence).
- A new or re-verified harness adapter, made with the `harness-onboard` skill — every manifest row
  carries version, date and how it was verified.
- A bundle catalog row, with its verdict added to [`docs/repo-survey.md`](docs/repo-survey.md).
- Workflow improvements that help most users of most stacks.

## What does not

- **Features that are too specialised or personalised** — one person's stack, tools, preferences,
  machine or company process. They belong in your fork, your `AGENTS.md` **RULES**, memory, or a
  domain master built with `domain-init`.
- Additions to the always-loaded `workspace_TEMPLATE/AGENTS.md` without a strong case: every token
  there is paid in every session by every user. Default to a skill.
- Anything naming a harness outside `adapters/`.

## How

1. Fork, branch `<type>/<short-slug>`, one topic per PR. Commits: `<type>: <subject>` — `feat` `fix`
   `docs` `refactor` `chore`.
2. Change `workspace_TEMPLATE/` or `adapters/`, never an installed copy; try it with `workspace-sync`
   on your own machine.
3. In the PR, say what changed and how you checked it: terms grepped, cross-references resolve,
   frontmatter well-formed, `README.md` still matches the template.

On Claude Code the repo's own skills need a link, since `.claude/` is gitignored:
`ln -s ../../.agents/skills/{name} .claude/skills/{name}` (Windows:
`mklink /J .claude\skills\{name} .agents\skills\{name}`).

Contributions are licensed under the repo's [MIT License](LICENSE).
