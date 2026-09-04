# Spec: U{N} — {Title}

> Behaviour + acceptance. The interface surface is the **stub files** named below — reference their
> names here, never restate a signature: the code is the contract, this is what it must do.

## Contract

The stub files this unit's surface lives in. Both halves build against these; neither may edit them.

- {real/path}: {the symbols declared there, one line}

Routes / CLI / other non-importable surface, reached through the entry point named above — or "none":

- `{METHOD} {route}` → `{request shape}` ⇒ `{response shape}`

## Scope

- {exactly what this unit creates or changes}

## Out of Scope

- {what this unit must not touch — behaviour owned by another unit}

## Behaviour

- {observable behaviour, concrete enough to implement against}
- {error / edge cases and their expected handling}

## Acceptance

1. {observable condition that must hold — the test-author pins each of these}
2. ...

## Consumes from prior units

- {interface line this builds on — copy from STATE Interfaces} — or "none"

## Key Files

- {path}: {create | modify — what}
