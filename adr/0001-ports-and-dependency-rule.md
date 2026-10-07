# ADR-0001: Hexagonal layout and an enforced dependency rule

- Status: accepted
- Date: 2026-10-03
- Tasks: M1.1, M1.2

## Context

Krama has a core (runs, decisions, plans, budgets) and many things that will change or be replaced: databases, agent transports, artifact storage, the UI and the CLI. Contributors need to know where code goes, and the core must stay testable without any of them.

## Decision

A pnpm and Turborepo monorepo with a pure `contract` (zod schemas and the route table), an `engine` (domain, use cases, and **ports** such as `Store`, `EventLog`, `AgentGateway`), and adapter packages that implement those ports. `contract` imports nothing internal; `engine` imports `contract` only; adapters import `contract` and `engine`, never each other; only `apps/server` wires adapters to ports; `apps/ui` and `apps/cli` import `sdk` and `contract` only. The rule is checked in CI with dependency-cruiser. Every port ships a reusable contract test suite that each adapter must pass.

## Consequences

- The engine runs in tests with in-memory fakes, and a new adapter is accepted when it passes the shared suite.
- A change that wants a forbidden import has to add a port instead, which keeps coupling visible.
- More packages and wiring code than a single app would have.
