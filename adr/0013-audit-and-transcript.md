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

## Update (2026-10-07): the storage decision, from a spike (M2.5)

**What was built.** A hash-chained, append-only ledger: each record carries the hash of the one before and its own hash over a canonical form of every other field, with a gapless position within its chain (one chain per run, one for control). Writers to one chain take turns on a lock on the chain's head row; writers to different chains do not meet. A record with a sender event id already in the chain is not written again. Triggers refuse `UPDATE`, `DELETE` and `TRUNCATE` on the records and let a chain's head move only forward. Each record is stored as the exact text that was hashed (not as `jsonb`, which reorders keys and rewrites numbers). `verify` finds a record whose content changed, a record that was removed or moved, a tail that was cut off, and a missing or altered blob. The same shared test suite passes on the embedded database and on PostgreSQL.

**Where it lives.** The default is **the main database**, with a configuration switch to keep the audit record in **a separate embedded database**. A development-machine spike measured appends of 0.3 to 8 KB payloads (random text, a worst case for size):

| | Embedded (PGlite) | PostgreSQL 16 |
|---|---|---|
| Appends per second, one chain | about 250 | about 240 to 360 |
| Appends per second, 4 and 8 chains at once | about 300 (no gain; one query at a time) | about 950 and 1,200 |
| Event-log append, alone / with audit writes in the same database / in a separate one (median) | 1 ms / 14 ms / 1.6 ms | 1.3 ms / 1.3 ms / 1.2 ms |
| Verification | 12,000 to 48,000 records per second | 19,000 to 54,000 records per second |

The embedded database runs one query at a time, so audit writes at full speed slow everything else on it; a separate database removes that. On PostgreSQL there was no measurable interference. Real load is far below these limits (tens of records per second), so the default stays simple (one database to back up) and the switch is there for heavy use.

**Size.** About 1 KB per record on top of the payload (the record, its hashes and four indexes): a 300 byte payload is about 1.3 KB, 2 KB is about 3.3 KB.

**Inline or blob.** Up to about 16 to 64 KB, writing the body inline and writing it as a file plus a reference cost the same (about 3 to 6 ms). Beyond that inline gets slow and bulky (15 ms at 256 KB, 47 ms at 1 MB, and the table grows by the body size) while a blob stays at about 5 ms and 1 KB. Bodies up to **16 KiB** are kept inline; larger ones are stored by their content hash on disk.

**What this does not give.** Triggers stop the application and ordinary users but not someone who can switch them off, which `verify` then detects. A tail removed together with the stored head is only detected against a head kept somewhere else (the control chain, an export manifest); sealing a run's final hash into the control chain is the next step. Writes are not yet grouped into one commit, which is the next lever if the embedded database ever limits throughput. Retention, deletion and export are not built yet.

## Update (2026-10-07): capture is built (M3.5)

Everything said and done in a run is now written to the run's chain before it is shown: the person's request, the orchestrator's tool calls and their results, decisions asked and answered, each delegation, every agent signal from the A2A stream and the HTTP sink (text and raw payload in full), and every request and frame the gateway sees. Writes to one run keep their order; the gateway side does not wait for the write, so a stream is not slowed.

**Secrets** are masked before hashing: values the platform knows (secrets it passes to agents, the tokens it issues) and credential-shaped strings (bearer tokens, API keys, private keys). A record says that it was masked and by which rules. Masking is not reversible, and a secret in a form the rules do not know is not caught.

**Findings** are written when a run closes: an agent that was meant to report to the sink, was seen working and never did; a start with no end; records that could not be written. An event that cannot be attributed to a run goes to the control chain with what it said. A run then ends with a summary record. If a record cannot be written the run carries on, the failure is counted and reported, and a stricter mode that stops the run is available.

**Not yet covered:** the same event delivered on two channels is recorded twice (the wrapper does not put its event id on the A2A copy); control records of who did what (identity is a later task); retention and export; a crash can lose writes that were still queued.
