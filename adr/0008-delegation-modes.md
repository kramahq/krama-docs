# ADR-0008: Delegation is native by default and can run through Krama

- Status: accepted
- Date: 2026-10-03
- Tasks: M4.5, M4.6, M4.8

## Context

Claude Code and similar tools do not speak A2A; they delegate through MCP tools. The wrapper can already turn A2A sub-agents into MCP tools. Krama can also relay delegation itself, which gives it a direct view of every exchange.

## Decision

Two modes, chosen per run (built-in default, then platform policy, then the run request): **`native`** (default) gives the orchestrator its sub-agents as MCP tools generated from the pack's agent graph, so delegation is the wrapper's own mechanism; **`krama`** relays each delegation through Krama's orchestration tools. In native mode Krama still receives the sub-agents' events through the event sink (ADR-0010).

## Consequences

- Native mode uses proven wrapper behaviour; relay mode gives Krama in-line control.
- Usage from native workers is visible only through the sink, so the sink must be configured for them.
- Two code paths to keep equivalent: a demo test runs the same scenario in both modes.
