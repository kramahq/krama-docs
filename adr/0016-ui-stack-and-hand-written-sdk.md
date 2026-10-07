# ADR-0016: React, Vite and Tailwind for the UI, and a small hand-written SDK

- Status: accepted
- Date: 2026-10-05
- Tasks: U1

## Context

The UI is the main way people see how agents are wired, so it must be quick to change and test, and it must depend on the same contract as the CLI.

## Decision

React 19, Vite, Tailwind v4 with design tokens as CSS variables (light and dark), TanStack Router and Query, and Vitest with Testing Library. The UI imports only the `sdk` and `contract`. The SDK is a small hand-written typed client with a fetch-based event stream (reconnect, resume, resnapshot). A mock API with seed data lets the UI be built and tested before the server is complete.

## Consequences

- Changing the contract breaks the UI at compile time.
- The SDK covers only what the UI needs so far and is not generated; generating it from the route table is possible later.
- The visual design is provisional and will move to a shared design system when one is adopted.
