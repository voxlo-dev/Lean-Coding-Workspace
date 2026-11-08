# Global Claude Code Workspace

@./AGENTS.md
@~/.agents/memory/MEMORY.md

`AGENTS.md` beside this file is the harness-neutral source of truth — edit it, not this file, which
exists only because Claude Code loads `CLAUDE.md` and resolves `@` imports.

## Claude-Code-specific overrides

**Plan approval:** before any plan-approval prompt, explain the plan in a visible message — what changes, where, why, in which order. A bare "implement this plan?" without that context is never enough.
