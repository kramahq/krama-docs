# ADR-0024: TypeScript, pnpm with Turborepo, changesets, Apache-2.0 with DCO, and the names

- Status: accepted
- Date: 2026-10-03
- Tasks: M0.1, M0.4

## Context

A new open-source project needs a small set of foundations settled once.

## Decision

TypeScript (strict) on Node 22 and newer. A pnpm workspace with Turborepo, versioned with changesets. Apache-2.0 licence, contributions signed off with the DCO, no CLA. GitHub organisation `kramahq`; npm package `kramahq` for the CLI and `@kramahq/*` for libraries; the command is `krama`; the Python package is `krama`.

## Consequences

- Familiar tooling for contributors.
- Every commit needs a sign-off (`git commit -s`), checked in CI.
- Names are claimed, so renaming later would be costly.
