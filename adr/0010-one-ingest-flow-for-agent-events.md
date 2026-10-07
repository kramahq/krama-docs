# ADR-0010: One normaliser and one ingest flow for the A2A stream and the HTTP event sink

- Status: accepted
- Date: 2026-10-05
- Tasks: M3.3, M4.8

## Context

Wrappers report thinking, tool calls, status and usage on the A2A stream, and can also POST the same events to an HTTP endpoint. Two code paths would drift and count usage twice.

## Decision

Both channels are mapped by one normaliser into one internal signal shape and go through one `ingest` use case. The sink is a loopback endpoint (`POST /agent-events`) with a per-instance bearer token, a size cap, de-duplication by event id, and attribution by the event's own correlation context first and the token's claims second; an event that cannot be attributed is counted and parked, never guessed. The sink is used only for agents Krama does not call itself, so usage is counted once.

## Consequences

- Downstream code sees one shape whichever channel carried the event.
- Loopback only: remote agents would need an authenticated remote ingest.
- The normaliser currently **clips** long text and raw payloads, which suits the activity feed but not audit (ADR-0013).
