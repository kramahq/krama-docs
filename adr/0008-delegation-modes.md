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

## Runtime observations (2026-10-11)

On macOS, Codex `1.7.3-canary-20261011154949` called Copilot `1.8.3-canary-20261011154949` through the generated native bridge, using existing local subscription/GitHub authentication. A proof read from the worker's isolated workspace was returned by Codex. Synchronous calls completed in about 17–25 seconds. With a 1 ms bridge sync budget, Codex successfully used task-result/status polling and returned the proof in about 20–22 seconds. This verifies the startup and bridge approval fixes in [wrapper PR #55](https://github.com/shashikanth-gs/a2a-wrapper/pull/55).

Remaining integration limitations:

- Krama's own governance MCP entry needs explicit headless Codex tool approval; the native bridge's approval does not apply to it ([Krama #43](https://github.com/kramahq/krama/issues/43)). Adding the documented approval setting made a real `get_run` call succeed.
- Copilot worker tool activity reached the HTTP sink, but lifecycle and usage did not. Usage was present in its direct terminal A2A response ([wrapper #56](https://github.com/shashikanth-gs/a2a-wrapper/issues/56)).
- Skillmap `0.2.1` did not preserve the initial worker conversation. An explicit, programmatic follow-up with the returned session id opened another worker context; artifact mode also omitted that id ([skillmap #2](https://github.com/shashikanth-gs/a2a-mcp-skillmap/issues/2)).
- Worker events did not inherit caller trace/run/phase metadata; per-instance token claims attributed them to the run ([wrapper #49](https://github.com/shashikanth-gs/a2a-wrapper/issues/49)).

The native default remains in force. These probes do not establish a complete pack workflow, long-job relay timeout, or Windows/Linux behavior. M4.5 remains partial until the outstanding reporting/continuity work and remaining measurements are resolved.
