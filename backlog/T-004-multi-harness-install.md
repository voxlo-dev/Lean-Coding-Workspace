# T-004 — Multi-harness install targets

- **Summary:** `workspace-install` also writes the harness-specific targets OpenCode needs — agent definitions today, whatever the next harness needs later
- **Category:** feature
- **Importance:** medium
- **Effort:** S
- **Depends on:** none

## Why

The workspace is Claude-Code-shaped by install, not by content. OpenCode already reads
`~/.claude/skills/<name>/SKILL.md` directly, so every skill works there unchanged — but **agent
definitions do not carry over**: OpenCode looks in `~/.config/opencode/agents/` and never in
`~/.claude/agents/`. `localagent-workflow` is the first skill whose agents must be registered with
the harness to work at all; unregistered there is nothing to dispatch and the model runs every step
in one context, which is exactly the failure the workflow exists to prevent.

Copying seven files by hand per machine is the kind of step that silently rots — an agent gets
edited in the template and the OpenCode copy keeps running the old prompt, with no signal that the
two diverged.

## What

`workspace-install` gains a second install target, so one run leaves both harnesses consistent:

- Agent definitions from `.claude_TEMPLATE/agents/` land in `~/.claude/agents/` **and**
  `~/.config/opencode/agents/`, **flattened** — the group folders are a repo convenience, and a nested
  install changes the agent's id in OpenCode. The files are already dual-dialect — merged frontmatter
  that each harness reads its own keys from, so this is a copy and not a transform.
- Skip the OpenCode target when that config directory does not exist; installing a harness the user
  does not have is noise, not service.
- Same repair semantics as the rest of the skill: re-running syncs and reports what changed, and
  never touches user-owned files.

Then say so where it matters: `README.md` currently presents the workspace as Claude Code only, and
that stops being true here.

## Open questions

- Which skills beyond `localagent-workflow` ever ship agents? While it is the only group, the flat
  copy needs no name-collision rule; a second group would want one.

**Answered:** OpenCode scans its agents directory recursively **but the path below `agents/` becomes
the agent ID** (`agents/team/reviewer.md` → `team/reviewer`), while Claude Code scans recursively and
keys off the `name:` frontmatter instead. So the two harnesses would disagree on an agent's name in
any nested install. **Install flat in both**, whatever the source layout is.
