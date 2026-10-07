# ADR-0020: Remote third-party A2A agents can be members of a pack

- Status: proposed
- Date: 2026-10-07
- Tasks: M6.1

## Context

Not every useful agent is a wrapper that Krama starts. A research agent, for instance, may run elsewhere and expose A2A. A pack author should be able to include it.

## Decision

A pack's agent catalogue can name an **external** agent by its A2A address. Krama does not start or stop it; it reaches it through the same gateway and, for callers that cannot speak A2A, through the skill-map (ADR-0008). Orchestrators are always managed agents, not external ones. Credentials for an external agent come from the secret store, never from the pack.

## Consequences

- Packs can use agents Krama does not control.
- Krama cannot see inside an external agent: usage and events appear only if it reports them, and audit records the exchange at the gateway (ADR-0013).
- Availability and trust of external agents become the pack author's and administrator's concern; allow-lists apply.
