# ADR-0029: Speak A2A v1.0 natively through the official SDK; 0.3 only for older agents

- Status: accepted
- Date: 2026-10-07
- Tasks: M3.6

## Context

The a2a-wrapper core speaks A2A v1.0 natively, built on the official `@a2a-js/sdk` v1, and stays compatible with v0.3 clients through the SDK's own compatibility layer. It chooses the wire format per request from the `A2A-Version` header; a request with no header gets the 0.3 shape. Krama's first gateway sent no version header and used 0.3 method names, so it worked only through that fallback, did not read the agent card, and did not use v1 operations. It was recorded as ADR-0026, which was wrong.

## Decision

Krama's gateway is **A2A v1.0 native**. It uses the official `@a2a-js/sdk` v1 client behind the `AgentGateway` port, discovers agents from their agent card (`supportedInterfaces`), sends `A2A-Version: 1.0`, and uses the JSON-RPC and HTTP+JSON bindings (gRPC is not used). It uses the v1 operations `SendMessage` and streaming send, `SubscribeToTask`, `GetTask`, `ListTasks` and `CancelTask`. Version 0.3 is supported only through the SDK's compatibility layer, for older external agents. Outbound calls to external agents are hardened (no redirects, size and time limits, origin allow-list, address checks, credentials bound to origins).

## Consequences

- Krama uses the same protocol surface as the wrappers and other v1 agents, including task subscription and listing.
- It depends on the official SDK, which adds a dependency to the adapter; the port keeps the engine free of it.
- Older 0.3-only agents depend on the SDK's compatibility; they are tested with a 0.3-only fake server.
- Supersedes ADR-0026.
