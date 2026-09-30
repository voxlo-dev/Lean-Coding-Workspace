# Security

## Use at your own risk

The workspace is provided **as is, without warranty** ([MIT License](LICENSE)). It instructs coding
agents that run shell commands, install third-party skills, plugins and MCP servers, keep tokens in
your environment, change files and git history and — through `release` — publish software and touch
live environments. Review what an agent proposes before approving it, and keep backups.

Third-party content it can install (skill bundles, plugins, MCP servers such as codegraph and
context7) is not reviewed by this project and carries its own license and risks.

## What the workspace does to limit risk

Safeguards, not guarantees:

- `workspace-sync` never overwrites a file it did not write without asking, and offers a backup first.
- Skill bundles install at a pinned commit; an update shows the upstream diff, and a bundle's own
  scripts are shown before they run.
- Project MCP servers are approved by name, never wholesale.
- GitHub access defaults to a fine-grained token scoped by you.
- `release` gates on dependency audit, secret scan, a proven backup and a migration test, and
  refuses a production target until you have seen and accepted its risks.
- Fetched web and marketplace content is treated as data, never as instructions.

## Reporting a vulnerability

Use GitHub's private vulnerability reporting (**Security** tab → **Report a vulnerability**), not a
public issue. Expect a reply on a best-effort basis.
