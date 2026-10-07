# ADR-0007: An agent is a directory holding the wrapper configuration

- Status: accepted
- Date: 2026-10-03
- Tasks: M3.4, M4.8, M6.1

## Context

Agents must be configurable by people, shareable through git, and creatable in a UI, without Krama inventing a second configuration language.

## Decision

An agent is a **directory**: the wrapper's JSON `config.json` plus the files it references (instructions, skills). Krama does not translate it; it validates it against the wrapper's schema and derives a per-run copy with Krama's overrides (ports, tokens, workspace, event sink) applied on top. The original is never modified.

## Consequences

- What works in the wrapper works in Krama, and the Studio can edit the same files.
- Krama follows the wrapper's schema as it evolves; a published schema from the wrapper would remove the risk of drift.
- Derived files contain run-specific values and are removed when the agent stops.
