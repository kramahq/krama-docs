# Architecture decision records

An ADR records one decision: the situation that forced it, what we chose, and what follows from it. They are the reasoning behind Krama's design and the source for the user documentation, so each one is written for a reader who was not there.

## How we use them

- **One decision per record.** Number them in order; never reuse a number.
- **Write one when a task makes a real choice** between options, changes an earlier decision, or when a spike produces numbers that justify one. Do it in the same change as the code.
- **Status:** `proposed` (agreed in principle, not built), `accepted` (built or in force), `superseded by ADR-NNNN`, or `rejected`. Change the status when the work lands.
- **Records are not rewritten.** If a decision changes, add a new ADR that supersedes it and mark the old one. Small corrections (typos, links, a later fact in Consequences) are fine and should be dated.
- **Link the work.** `Tasks:` lists the plan task IDs; the pull request that lands the decision links the ADR.
- **Public text only.** An ADR states what and why in terms a user or contributor can read; it does not copy private planning material.

## Template

```markdown
# ADR-NNNN: Title (a decision, in a sentence)

- Status: proposed | accepted | superseded by ADR-NNNN | rejected
- Date: YYYY-MM-DD
- Tasks: M0.0

## Context

What forces the decision. Facts, constraints, what we tried.

## Decision

What we do, in the active voice.

## Consequences

What gets easier, what gets harder, what we now have to keep true.
```

## Index

| ADR | Decision | Status |
|---|---|---|
| [0001](./0001-ports-and-dependency-rule.md) | Hexagonal layout and an enforced dependency rule | accepted |
| [0002](./0002-cross-platform-and-single-entry-point.md) | Windows, Linux and macOS are first-class; one entry point | accepted |
| [0003](./0003-agents-as-wrapper-processes-over-a2a.md) | Agents are A2A wrapper processes that Krama starts and talks to over A2A | accepted |
| [0004](./0004-persistence-pglite.md) | Embedded Postgres (PGlite) locally, Postgres when hosted | accepted |
| [0005](./0005-replayable-event-log.md) | A durable, cursor-ordered event log with resumable streaming | accepted |
| [0006](./0006-pack-format-orchestrator-and-agent-catalogue.md) | A pack is one YAML: an orchestrator and a catalogue of agents wired by hints | accepted |
| [0007](./0007-agent-is-a-wrapper-config-directory.md) | An agent is a directory holding the wrapper configuration | accepted |
| [0008](./0008-delegation-modes.md) | Delegation is native by default and can run through Krama | accepted |
| [0009](./0009-agent-graph-leaf-first-start.md) | Resolve the agent graph, start leaves first, share one instance per agent per run | accepted |
| [0010](./0010-one-ingest-flow-for-agent-events.md) | One normaliser and one ingest flow for the A2A stream and the HTTP event sink | accepted |
| [0011](./0011-stable-agent-addresses.md) | Keep agent addresses stable; health-check and restart instead of re-addressing | accepted |
| [0012](./0012-cost-is-provider-reported.md) | Cost is what the provider reported, or null | accepted |
| [0013](./0013-audit-and-transcript.md) | A lossless transcript and a hash-chained control audit | proposed |
| [0014](./0014-identity-and-roles.md) | Every command carries a principal; roles are minimal; not multi-tenant | proposed |
| [0015](./0015-git-sources-and-drafts.md) | Git-based sources using the user's own git; local-git drafts; immutable published versions | proposed |
| [0016](./0016-ui-stack-and-hand-written-sdk.md) | React, Vite and Tailwind for the UI, and a small hand-written SDK | accepted |
