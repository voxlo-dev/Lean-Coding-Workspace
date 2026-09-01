# Contract: U{N} — {Title}

> The exact interface surface. The `test-author` and the `implementer` each build from this **without seeing each other's work** — so it must be precise enough that their outputs fit together. Names, signatures, and paths are binding; behaviour lives in `spec.md`.

## Exposes

Every function / type / endpoint this unit adds, with its full signature.

- `{name}({params with types}): {return type}` — at `{path}`
- `type {Name} = { {field: type}, ... }` — at `{path}`
- `{METHOD} {route}` → `{request shape}` ⇒ `{response shape}` — at `{path}`

## Error / boundary shapes

- {error or exception type, and when it is raised}
- {data shape crossing the boundary}

## Consumes from prior units

- {interface line this builds on — copy from STATE Interfaces} — or "none"

## Paths

- {path}: create | modify
