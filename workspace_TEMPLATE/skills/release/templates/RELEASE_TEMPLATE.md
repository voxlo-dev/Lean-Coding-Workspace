# Release {version}

<!-- CONTRACT (binding — delete this comment once the release is filled):
  - Template: `~/.agents/skills/release/templates/RELEASE_TEMPLATE.md`.
  - Lives at `artefacts/release-{version}/release.md`. Ephemeral tier: live for the run,
    frozen the moment the release lands or fails.
  - THE STATE TABLE IS THE RESUME CONTRACT. Tick a step only once its effect is real, and
    re-read the table before every step — a repeated push, tag or submission cannot be undone.
  - The changelog below is the ONLY changelog: PR body and GitHub release, never a repo doc.
    The sprint archives plus the tag are the repo's record.
  - Writer: `release`. Nothing else edits this file.
-->

- **Version:** {x.y.z} {· versionCode NNN, where the ecosystem carries one}
- **Previous tag:** {v{x.y.z} — or `none`, first release}
- **Sprints in scope:** {slug, slug, …}
- **Targets:** {CI pipeline | GitHub release | domain store — one or more}

## State

| Step | Status | Evidence |
| --- | --- | --- |
| 0 preflight | {open/done} | {auth ok, tree clean, scope resolved} |
| 1 changelog | {open/done} | — |
| 2 version bump | {open/done} | {commit sha} |
| 3 security & data gate | {open/done} | {audit, secret scan, backup + restore, migration test} |
| 4 release PR | {open/done} | {PR url} |
| 5 CI | {open/done} | {run url} |
| 6 audit | {open/done} | {review url, blocking findings} |
| 7 ship | {open/done} | {run / release url / submission id} |
| 8 land | {open/done} | {merge sha, tag} |

## Changelog

{Keep a Changelog groups, composed from the sprint boards, their `sprint-decisions.md` and the
`spec_*` behaviour deltas. The user's view of each change, not a git-log dump. Drop any empty group.}

### Added
### Changed
### Fixed
### Removed

{Breaking changes get their own call-out with the migration step, above the groups.}

## Gates

- **Dependency audit:** {command, result — only `low` passes}
- **Secret scan:** {clean, or what was found and how the credential was rotated}
- **Backup:** {command, where it is stored, restore proven on — or `n/a`, no persistent state}
- **Migration test:** {forward · rollback · suite on the migrated copy — or `n/a`}
- **Production go:** {the user's risk acceptance, verbatim and dated — or `n/a`, no production target}
- **Waivers applied:** {advisory IDs carried from `docs/release.md` — or `none`}
- **Audit findings:** {blocking ones and how they were resolved — or `none`}

## Outcome

{`released` with the tag and target links · or `failed`: what broke, what was reverted, the
ticket that carries the fix. A published version is never unpublished — the next one fixes forward.}
