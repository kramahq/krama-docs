# ADR-0003: Agents are A2A wrapper processes that Krama starts and talks to over A2A

- Status: accepted
- Date: 2026-10-03
- Tasks: M3.1, M3.2

## Context

Krama coordinates agents on several backends (Claude, Codex, Copilot, OpenCode, Antigravity). Each is exposed as an A2A server by its own wrapper package. An earlier prototype embedded one wrapper inside the server process, which tied it to one backend and shared its failure domain.

## Decision

Krama starts **one child process per agent instance** from the wrapper's CLI, with a per-agent configuration, and talks to it through an `AgentGateway` port that speaks A2A JSON-RPC. The backend is detected from the config and described by a registry of backend descriptors, so supporting another backend means adding a descriptor, not engine code.

## Consequences

- Any backend that has a wrapper works the same way, and a crashing agent cannot take the server down.
- Krama owns process lifecycle: ports, health, restart, cleanup.
- A process per agent costs memory and start-up time; embedding remains possible behind the same port if that ever matters.
