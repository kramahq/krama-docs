# ADR-0012: Cost is what the provider reported, or null

- Status: accepted
- Date: 2026-10-03
- Tasks: M3.3

## Context

Estimated prices go stale and mislead, and users and auditors compare them with real bills.

## Decision

Usage is recorded in the units the provider reports (tokens, calls, provider credits). Cost is shown only when a provider reports it, otherwise it is null. Krama never estimates a price. Budgets apply to reported units.

## Consequences

- Numbers match the provider's own.
- Some runs show no cost; the UI says "not reported" rather than guessing.
