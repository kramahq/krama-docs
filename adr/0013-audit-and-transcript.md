# ADR-0013: A lossless transcript and a hash-chained control audit

- Status: proposed
- Date: 2026-10-07
- Tasks: M2.5, M3.5, M5.5

## Context

Organisations that run Krama need to show what was said and done in a run, who did it, and that the record was not altered. The event log (ADR-0005) is a retained feed with clipped payloads and no acting user, so it cannot serve as the record.

## Decision

Two append-only, hash-chained records behind an `AuditLog` port. The **transcript** is lossless, per run: every user, orchestrator and agent message, thinking, and each tool request and response, captured at the gateway, from the wrapper's stream and sink, and from Krama's own interfaces, de-duplicated, with gaps recorded as findings. Large bodies are stored by content hash. The **control audit** records who did what (decisions, installs, permissions, policy, exports). Records are written before they are published, streamed live per run, exportable as a verifiable bundle, and secrets are masked before hashing. Storage defaults to the same database with a switch to a separate one; a one-day spike (M2.5) measures volume and settles this.

## Consequences

- Lossless capture costs storage; retention, legal hold and deletion tombstones manage it.
- The activity feed remains a clipped view that links to the full record.
- Open points are listed in the spec and will be settled as the tasks land.
