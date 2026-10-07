# ADR-0019: Agents and packs are two layers: reusable agent definitions, and packs that wire them

- Status: accepted
- Date: 2026-10-07
- Tasks: M3.4, M4.8, M6.1

## Context

Defining an agent (backend, model, instructions, tools, skills) is a different job from deciding how a team of agents works. The same reviewer or researcher agent is useful in many packs. Definitions should be maintainable by many people in git, with some provided out of the box.

## Decision

Layer one is the **agent**: a wrapper configuration directory (ADR-0007), kept in a git repository that people maintain, with a set provided by the project. Layer two is the **pack**: it picks an orchestrator and agents from those definitions (from git, embedded, or external) and wires them with hints (ADR-0006). Krama starts agents from their definitions and manages the pool of processes and ports.

## Consequences

- Agents are reused across packs and can be improved independently.
- A pack pins the versions of the agents it uses (ADR-0015), so a change in a shared agent does not silently change an old pack.
- Two places to look when something behaves unexpectedly; the Studio and the run view show the resolved result.
