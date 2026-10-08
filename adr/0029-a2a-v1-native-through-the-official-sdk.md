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
- 2026-10-07, as built (M3.6): discovery sends `A2A-Version: 1.0` because the wrappers serve a 0.3 card to a caller that does not. Agents Krama started are reached with a managed policy (loopback allowed); agents it did not start (`external`) are reached on public addresses only, checked when the socket connects. A card may point only at its own origin unless the operator allow-lists another. The card is cached for five minutes. A single malformed event frame in a stream is skipped, not fatal. Streaming falls back to one `SendMessage` when the card does not advertise it. Not yet done: `SubscribeToTask`, `GetTask` and `ListTasks` (M3.7), OAuth and mTLS credential types, signed-card verification.
- 2026-10-08, as built (protocol gaps): the gateway now has `GetTask`, `ListTasks` and `SubscribeToTask` (subscribing only observes and never sends a message; a finished task yields its final state read with `GetTask`). Authentication to an external agent is OAuth 2.0 client credentials or a fixed bearer, bound to one origin, with the token cached and renewed once on a 401; it is applied in the guarded fetch so the token endpoint obeys the same egress rules. Mutual TLS and a private CA are set per origin. Signed agent cards are verified by the SDK; Krama only decides which keys count (configured keys, optionally a key set from the signature's `jku` on an allowed origin) and whether a signature is required. A signature that fails is always refused. Proxy variables are honoured for external agents by default and never for agents Krama started. **Consequence:** through a proxy the connect-time address check cannot run, because the proxy resolves the name; the origin allow-list, the literal-address check, no redirects and size caps still apply, and `proxy: none` restores the full check. Signed cards are checked on the v1 card; fields outside the v1 schema are not covered by a signature. OAuth is client credentials only. These options are not yet read from configuration.
