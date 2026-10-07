# ADR-0005: A durable, cursor-ordered event log with resumable streaming

- Status: accepted
- Date: 2026-10-03
- Tasks: M2.2, M5.3

## Context

The UI and other clients need to follow runs live, survive a dropped connection, and see the same history after a restart.

## Decision

Every state change and activity item is an `EventEnvelope` in a persisted log with a monotonic cursor, per-topic filtering and retention. Clients stream it over server-sent events and resume with `last-event-id`; if the server no longer holds events back to the cursor it answers 410 and the client takes a fresh snapshot and reconnects. A heartbeat detects dead connections.

## Consequences

- Live views and recovery use one mechanism and are testable without a browser.
- Retention bounds growth, so the log is a **feed**, not an audit trail (see ADR-0013).
- Clients must implement resume and resnapshot; the SDK does this once for everyone.
