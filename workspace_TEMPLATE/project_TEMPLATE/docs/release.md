# Release Runbook — {project name}

<!-- CONTRACT (binding — sections are suggestions, this header is not. Delete this comment
     once the doc holds real content; template: `~/.agents/project_TEMPLATE/docs/release.md`):
  - PURPOSE: everything `release` needs and cannot derive. The skill STOPS if this doc is missing.
  - THE AUTHORITY: this doc carries every shipping rule — version carriers, audit command,
    build, publication, workflow names, secrets, store listing, environments. An installed
    domain's `Domain-Recipe.md` → Engineering seed only filled it once, at init.
  - OPTIONAL doc, kept only where the project actually publishes. No releases → delete it.
  - SYSTEM-INDEPENDENT ONLY, like `dev.md`: credential *names* and where they are configured,
    never their values; local paths and personal tooling go to project memory.
  - BOUNDARIES: day-to-day build/debug → `dev.md`; signing, tracks and store metadata → here.
  - WHO WRITES: the user, or `release` when it finds the doc missing. Waivers are appended by
    `release` and re-checked every run.
-->

## Version

{every file and field carrying a version, so all of them get bumped in one commit. Note any
second number that must increase strictly per upload.}

## Targets

{which of CI pipeline / build + GitHub release / store publication this project uses, in order.}

### CI pipeline

{workflow file, how it triggers, the secrets and environments it needs, where output lands.}

### Build

{the build command and artifact paths.}

### Store / registry

{what is specific to *this* project — the listing, review expectations, metadata that has to be
updated per release. Track order and signing rules belong here too.}

## Security gate

{the dependency-audit command. Only `low` findings pass.}

### Waivers

{one entry per advisory with no upstream fix: ID · why it isn't reachable here · date · the
release that last confirmed it. Delete an entry the moment it stops being true.}

## Preconditions

{what must be true before a release starts and isn't checkable from the repo — configured
secrets, store account state, an environment that has to be up.}
