# ADR-0011: Keep agent addresses stable; health-check and restart instead of re-addressing

- Status: accepted
- Date: 2026-10-05
- Tasks: M4.8

## Context

An agent that dies and returns on another port leaves its callers with stale addresses. Callers inside a wrapper hold the address in their configuration.

## Decision

A restarted agent keeps its port when it is free. At each orchestrator turn Krama checks health and revives an agent in place; if an address does have to move, its callers are restarted (cascade) and a replaced orchestrator gets a resume notice. A proxy that resolves agents to addresses (so a downstream can move transparently, and so remote agents work) is deferred and tracked as future work.

## Consequences

- Simple and sufficient for one machine.
- A moved address costs caller restarts.
- Distributed deployments need the proxy or a registry; the port is the extension point.
