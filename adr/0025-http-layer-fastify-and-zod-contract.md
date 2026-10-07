# ADR-0025: Fastify with zod schemas from the shared contract for the HTTP API

- Status: proposed
- Date: 2026-10-03
- Tasks: M1.3, M5.1

## Context

The API is described once in the `contract` package (zod schemas and a route table) and used by the server, the SDK, the mock and the UI.

## Decision

The HTTP layer is Fastify 5 with request and response validation from the contract's zod schemas, and an OpenAPI description generated from them. The mock API already uses Fastify; the real server adopts it in M5.1, which confirms or amends this record.

## Consequences

- One source of truth for shapes, validated on both sides.
- The server and the mock cannot drift from the contract without a type or test failure.
- Locks the HTTP layer to one framework.
