# ADR-0009: Resolve the agent graph, start leaves first, share one instance per agent per run

- Status: accepted
- Date: 2026-10-05
- Tasks: M4.8

## Context

With sub-agents wired by address, a caller needs its callees' addresses before it starts. A shared agent used by two callers must not run twice.

## Decision

The runner resolves the pack's agent graph (validating ids, references and cycles), starts agents **leaf first**, passes each caller the addresses of its callees, and shares **one instance per agent per run**. Each instance gets a derived per-run config (file mode 0600) and its hints are rendered into its callers' prompts. An invalid graph fails with a clear error before anything starts.

## Consequences

- Addresses are known at start, with no fixed endpoints in packs.
- Cycles are rejected rather than half-started.
- A `git` source fails with a clear message until git sources are implemented (ADR-0015).
