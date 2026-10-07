# ADR-0028: Usage uses OpenTelemetry generative-AI naming where applicable

- Status: proposed
- Date: 2026-10-03
- Tasks: M3.3, M5, M12

## Context

The wrappers report usage with OpenTelemetry generative-AI semantic conventions. Using the same names makes Krama's usage data comparable and exportable to standard tools. Today Krama stores usage in contract units (tokens, calls, provider credits).

## Decision

Krama maps the wrappers' usage into its contract units and keeps the original names where they exist, and exports traces and metrics using the OpenTelemetry generative-AI conventions. Full export is not built yet; this record sets the direction and is confirmed when it is.

## Consequences

- Easy integration with existing monitoring.
- The conventions are still evolving, so the mapping needs maintenance.
- Cost remains provider-reported only (ADR-0012).
