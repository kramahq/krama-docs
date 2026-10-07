# ADR-0017: Krama coordinates A2A wrapper agents; it does not reimplement them

- Status: accepted
- Date: 2026-10-07
- Tasks: M3, M4, W-track

## Context

The a2a-wrapper project already exposes coding agents (Claude, Codex, Copilot, OpenCode, Antigravity) as A2A servers, already reports thinking, tool calls and usage, and already supports sub-agents. The a2a-mcp-skillmap project already turns a remote A2A agent's skills into MCP tools for agents that cannot speak A2A natively, including long-running work. These are separate, published projects. The value Krama adds is the wiring around them, not a second copy of them.

## Decision

Krama **reuses** the wrapper and skill-map instead of rebuilding their features. Krama owns: starting and supervising agent processes, configuring the delegation between agents, running orchestrated work, collecting what the agents report, decisions, and the UI. When Krama needs something from a wrapper or the skill map (a bug fix or a capability), the need is filed as an issue in that project and Krama works with what exists meanwhile. Krama's configuration for an agent is the wrapper's own configuration (ADR-0007).

## Consequences

- Less code, and behaviour users already know from the wrappers carries over.
- Krama depends on the wrappers' releases and schemas; changes there can affect Krama, so versions are recorded and a published schema from the wrapper would help.
- Some features arrive on the wrappers' schedule, not Krama's.
