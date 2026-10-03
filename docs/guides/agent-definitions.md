# Agent definitions

An **agent definition** describes one kind of agent: what it is for, which [backend](./adding-a-backend.md) runs it, how it is configured, and what it may do. Packs ship definitions; you can also keep your own in a folder.

## Folder layout

```
agents/
  developer/              # role
    default/              # variant   ->  id "developer/default"
      agent.yaml          # the definition (or agent.json)
      prompt.md           # persona / system prompt (optional)
      context.md          # project context handed to the agent (optional)
    security-patch/
      agent.yaml
```

The id is `role/variant`, taken from the folder names (lowercase letters, digits and dashes).

## `agent.yaml`

```yaml
name: Developer
description: Implements units of work and writes tests.
backend:
  wrapper: a2a-codex          # any registered backend
  model: gpt-5-codex
  options:                    # provider-specific, checked against the backend descriptor
    sandboxMode: workspace-write
  common:                     # settings every wrapper shares: session, timeouts, logging, ...
    timeouts: { delegationMs: 1800000 }
  secrets:                    # environment variable -> secret reference (never the value)
    OPENAI_API_KEY: openai-main
capabilities: [code, tests]   # tags used to match this agent to a roster entry
mcpServers: [git]
permissions:
  tools: { shell: ask, write: allow, network: off }   # allow | ask | off
memory:
  enabled: true
  scopes: [{ scope: project, access: propose }]
costHint:
  perMillionTokens: { amount: 6, currency: USD }      # optional, used to rank cheaper first
```

When definitions load, each one is checked against the backend registry. You get the file path and the reason for every problem: an unknown backend (with the list of registered ones), an unknown or mistyped option, or a secret binding the backend never reads. A bad definition is skipped; the others still load.

## How a roster picks an agent

A pack's roster names roles. For each role Krama finds the best usable definition:

1. **Pinned definition.** `definitionId: developer/default` uses that definition. Adding `backend: a2a-claude` runs it on that backend instead.
2. **By capability.** Otherwise candidates are ranked: most requested capabilities covered first, then the preferred backend, then lower `costHint`, then id (so the result is stable). Definitions whose backend is unregistered or not allowed are never offered.
3. **Pinned backend.** A `backend` on a capability-based entry is a requirement, not just a preference.
4. **Optional roles** that nobody can fill are skipped. A required role that nobody can fill stops the run before it starts, and the error lists every unfilled role at once.
