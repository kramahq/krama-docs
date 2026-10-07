# ADR-0027: Identifiers are prefixed ULIDs

- Status: accepted
- Date: 2026-10-03
- Tasks: M1.1

## Context

Ids appear in URLs, logs, audit records and the UI. They should be sortable by creation time, easy to tell apart by kind, and need no coordination to create.

## Decision

Every entity id is a prefixed ULID, for example `run_01J…`, `dec_01J…`. The prefix names the kind and the ULID gives time ordering. The contract validates the shape.

## Consequences

- Ids are readable in logs and sort naturally.
- A leaked id says what kind of thing it is; ids are never secrets.
- Changing the format later would affect stored data and clients.
