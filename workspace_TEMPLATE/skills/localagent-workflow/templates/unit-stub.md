# Writing the stub — U{N}'s contract, as code

The contract is **source files in the repo's normal tree**, not a document. The `test-author` imports
them, the `implementer` fills them in; neither can misread a signature, because both compile against
the same declarations. Rules, not a fill-in template: the shape is the language's.

## What a stub is

Every symbol the unit exposes, declared at its **real final path** with its **full signature**, and a
body that does nothing but fail:

- Functions and methods: the exact name, parameters, types and return type; body raises the
  language's "not implemented" (`throw new Error("not implemented")`, `raise NotImplementedError`,
  `todo!()`, `panic("not implemented")`).
- Types, records, data shapes: **fully declared, real fields** — tests construct these, so a missing
  field breaks them.
- Enums, constants, error types: **real values**. A test may assert one.
- Every type used in a signature is declared here or is a language builtin.

## What a stub is not

- **No logic.** No branch, no loop, no validation, no default that encodes a decision. A body with an
  `if` in it is implementation, and it is the implementer's, written blind against this file.
- **No private helper.** Only the surface a caller or a test touches.
- **No placeholder path or name.** No `{...}`, no `foo`, no path you did not confirm exists or will.

## It must typecheck

Run the project's build/typecheck **before returning**: imports resolve, types line up, the file is
importable by a test. A stub that does not compile is guesswork both halves inherit differently.

## Docstrings — the only prose that lives here

One line per symbol: what it does, and what it raises and when. Behaviour, edge cases and acceptance
criteria belong in `spec.md`; do not restate them.

## Construction

For every type a test or caller must obtain, the stub says **how**: an exported constructor, a
factory function, or a plainly-constructible record. A type the stub does not answer "how do I get
one of these" for is one each blind half invents its own way to build.

## Surfaces that are not importable *(HTTP, sockets, CLI — skip if none)*

A route is not a signature. Declare the **importable entry point** that mounts or serves it as a
normal stub (`createServer(): Server`), and list the routes with their request/response shapes in
`spec.md`'s **Contract** section. A test reaches them through that entry point.
