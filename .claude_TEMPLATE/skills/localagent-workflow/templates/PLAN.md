# Plan: {Project Name}

Generated: {date}
Task: {one-line task summary}

## Target & Systems

- **Goal:** {what this builds, for whom}
- **Main systems / components:** {the parts involved}
- **Non-goals:** {explicitly out of scope}

## Features

- {feature 1}
- {feature 2}

## Stack

Binding for `localagent-scaffold`; decided with the user, not by an agent mid-run.

- **Language / runtime:** {and version}
- **Package manager:** {}
- **Test runner:** {and the command that runs it}
- **Key libraries:** {only what the features actually need — or "none"}
- **Already set up?** {what the repo already has, or "empty repo — scaffold from scratch"}

## Test Strategy

- **Levels that matter here:** {unit / integration / e2e — and why}
- **e2e surface?** {browser/UI or integration surface that justifies e2e — or "none"}
- **Existing tests to build on:** {paths, or "none"}

## Units

Small, independently implementable + testable. Keep them small — they bound every later agent's context. Each becomes one `spec.md` + `contract.md` and one blind implement/verify cycle.

| ID | Title | Scope (one line) | Depends on |
| --- | --- | --- | --- |
| U1 | {title} | {what it does} | — |
| U2 | {title} | {what it does} | U1 |

## Open Questions

- {anything that needs a human decision before building — or "none"}
