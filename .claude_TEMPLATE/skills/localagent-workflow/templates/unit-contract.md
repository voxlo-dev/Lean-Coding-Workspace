# Contract: U{N} — {Title}

> The exact interface surface. The `test-author` and the `implementer` each build from this **without
> seeing each other's work** — so it must be precise enough that their outputs fit together. Names,
> signatures, and paths are binding; behaviour lives in `spec.md`. **No `{...}` placeholder may
> survive here** — every path is a real path, every type a real type. A caller cannot be written
> against a guess.

## Module exports

Called as `import { name } from "<path>"` — free functions, not members of anything.

- `{name}({params with types}): {return type}` — at `{real/path}`
- `{METHOD} {route}` → `{request shape}` ⇒ `{response shape}` — at `{real/path}`

## Types

Data shapes only. If a type carries callable members, list every one with its full signature — a
function is either a module export above or a member here, never left to the reader to decide.

- `{Name}` — at `{real/path}`
  - `{field}: {type}`
  - `{method}({params}): {return type}` — member, called as `instance.{method}(…)`

## Construction

How a caller obtains each type above — a constructor, a factory export, a plain object literal. Name
it, or the two blind halves will each invent their own.

- `{Name}` ← `{how}`

## Error / boundary shapes

- {error or exception type, its real name, and when it is raised}
- {data shape crossing the boundary}

## Consumes from prior units

- {interface line this builds on — copy from STATE Interfaces} — or "none"

## Paths

- {real/path}: create | modify
