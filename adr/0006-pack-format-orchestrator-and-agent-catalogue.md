# ADR-0006: A pack is one YAML: an orchestrator and a catalogue of agents wired by hints

- Status: accepted
- Date: 2026-10-03
- Tasks: M4.5, M4.8, M6.1

## Context

A pack defines how a use case is done by several agents. Earlier ideas (a fixed roster, methodology phases and gates written into the pack) baked one working style into the engine and made packs hard to author.

## Decision

A pack is one self-contained `krama-pack.yaml` that names **one orchestrator** and a **catalogue of agents** keyed by unique id. An agent comes from `config` (an embedded wrapper config), `git` (a wrapper config directory at a pinned version) or `external` (a remote A2A agent). Wiring is `subAgents: [{agent, hint}]`, where the hint tells the caller when to use that agent. The orchestrator decides and records the plan; phases appear as the orchestrator plans them. Any agent can be the orchestrator on any backend that supports it.

## Consequences

- Authoring a pack is mostly listing agents and describing how they relate.
- The engine carries no methodology; process lives in prompts and hints.
- The engine still reads the older methodology and roster until they are removed (M4.9).
