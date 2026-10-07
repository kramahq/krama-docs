# ADR-0022: The Studio authors agents and packs in the UI and stores them in git

- Status: proposed
- Date: 2026-10-07
- Tasks: M10, U6

## Context

Besides writing YAML and configs by hand, users should be able to create agents and packs in a UI and export them in the pack format. The result must be version-controlled and must match what the wrappers actually accept.

## Decision

The Studio edits **drafts** that are local git work trees (ADR-0015); a remote is optional for durability. It validates against the wrapper's real configuration schema and the pack schema, so what it produces runs. Publishing is an explicit step that commits and tags immutably. A built-in builder agent (an ordinary pack) can help author drafts. A draft may be tried in a run before it is published.

## Consequences

- Authors who prefer the UI and authors who prefer files work on the same artefacts, with full history.
- The Studio must follow the wrapper's schema as it changes.
- Drafts without a remote have local history only; the Studio says so.
