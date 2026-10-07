# ADR-0026: A2A 0.3 client now, behind the gateway port, with 1.0 later

- Status: accepted
- Date: 2026-10-03
- Tasks: M3.2, W7

## Context

The wrappers speak A2A 0.3 today and the protocol is moving to 1.0. Krama must not tie the engine to one protocol version.

## Decision

The gateway adapter speaks A2A 0.3 JSON-RPC (send, stream, task state) and sits behind the `AgentGateway` port. Moving to 1.0 is a new adapter or an update to this one, tracked as W7.

## Consequences

- The engine and use cases do not change when the protocol does.
- Features that only exist in 1.0 wait for that migration.
