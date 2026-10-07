# ADR-0014: Every command carries a principal; roles are minimal; not multi-tenant

- Status: proposed
- Date: 2026-10-07
- Tasks: M5.5

## Context

Audit has to say who acted, and approvals and installs need to be limited to the right people. Krama is run by an organisation itself, so multi-tenant hosting is not a goal.

## Decision

Every command carries a principal (id, display name, roles). Roles are `admin`, `operator`, `approver`, `viewer`. Locally the static token maps to one local principal; OIDC is added later behind the same shape.

## Consequences

- Audit records and approval checks have an actor from the start.
- No tenant boundary to design or test.
- Role granularity may need to grow (for example, transcript access as its own permission).
