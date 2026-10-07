# ADR-0021: Self-hosted by organisations, compliance-oriented, not a hosted service

- Status: accepted
- Date: 2026-10-07
- Tasks: M5, M9, M12

## Context

Krama is meant to be used inside organisations that run it themselves, on a developer machine or on their own infrastructure. "Enterprise" here means compliance, traceability and audit, not a central multi-tenant service. Some concerns that matter to a hosted product (tenants, a fleet scheduler) do not apply.

## Decision

Krama is local-first and self-hosted: one process tree per install, `npx kramahq` to start, PGlite by default and Postgres for an organisation that wants its own database. The deployment model is not multi-tenant. Compliance needs are met inside that model: a lossless audit and transcript (ADR-0013), identity and roles (ADR-0014), pinned and immutable sources (ADR-0015), secrets that never enter logs or repositories, and provider-reported cost only (ADR-0012). Runtime pieces that could later run elsewhere (the process manager, address resolution) stay behind ports so a container-based host or an address proxy can be added without changing the engine.

## Consequences

- Simple installs and a clear security story.
- No tenant isolation to design or test; running for several teams means several installs or projects with roles.
- Distributed deployment is possible later through the ports but is not a current goal.
