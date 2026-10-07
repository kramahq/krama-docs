# ADR-0018: Krama is use-case neutral: packs carry the use cases and any agent can take any role

- Status: accepted
- Date: 2026-10-07
- Tasks: M1, M6

## Context

The first use case is a software factory, but the aim is many use cases: an AI-DLC flow, a flow that hunts and fixes technical debt and vulnerabilities, a deck builder, and others. Roles differ per use case. Any backend can play any role: an orchestrator can be a Claude agent coordinating Codex agents, or all Claude, or any mix.

## Decision

The engine, contract and UI core hold no domain vocabulary (no software-development stages, no issue-tracker words). A use case is a **pack** (ADR-0006): its orchestrator, its agents, and the instructions and hints that describe how they work together. Which backend plays which role is the pack author's choice and is not fixed by the platform. Domain-specific adapters (for example a work-item source) live in packs or adapter packages, not in the core.

## Consequences

- One platform serves very different use cases, and new ones need no engine change.
- Quality of a use case depends on its pack and prompts, which therefore need their own tests (a pack testing kit is planned).
- A repository-wide check keeps domain words out of the core.
