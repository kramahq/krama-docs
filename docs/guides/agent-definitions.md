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
  wrapper: a2a-codex # any registered backend
  model: gpt-5-codex
  options: # provider-specific, checked against the backend descriptor
    sandboxMode: workspace-write
  common: # settings every wrapper shares: session, timeouts, logging, ...
    timeouts: { delegationMs: 1800000 }
  secrets: # environment variable -> secret reference (never the value)
    OPENAI_API_KEY: openai-main
capabilities: [code, tests] # tags used to match this agent to a roster entry
mcpServers: [git]
permissions:
  tools: { shell: ask, write: allow, network: off } # allow | ask | off
memory:
  enabled: true
  scopes: [{ scope: project, access: propose }]
costHint:
  perMillionTokens: { amount: 6, currency: USD } # optional, used to rank cheaper first
```

When definitions load, each one is checked against the backend registry. You get the file path and the reason for every problem: an unknown backend (with the list of registered ones), an unknown or mistyped option, or a secret binding the backend never reads. A bad definition is skipped; the others still load.

## How a roster picks an agent

A pack's roster names roles. For each role Krama finds the best usable definition:

1. **Pinned definition.** `definitionId: developer/default` uses that definition. Adding `backend: a2a-claude` runs it on that backend instead.
2. **By capability.** Otherwise candidates are ranked: most requested capabilities covered first, then the preferred backend, then lower `costHint`, then id (so the result is stable). Definitions whose backend is unregistered or not allowed are never offered.
3. **Pinned backend.** A `backend` on a capability-based entry is a requirement, not just a preference.
4. **Optional roles** that nobody can fill are skipped. A required role that nobody can fill stops the run before it starts, and the error lists every unfilled role at once.

## Install a wrapper configuration from Git

You can register a reusable agent without writing `agent.yaml`. The source directory contains the wrapper's own JSON `config.json` and any instruction or skill files it references. Krama detects and validates its backend, keeps the source at an exact commit, and starts a derived copy only when a run needs it. The existing local definitions remain supported.

First request a preview:

```http
POST /api/v1/agent-definitions/preview
Authorization: Bearer <token>
Content-Type: application/json

{
  "id": "reviewer/default",
  "source": {
    "url": "https://github.com/acme/agents.git",
    "ref": "main",
    "path": "reviewer"
  },
  "secrets": { "SERVICE_TOKEN": "service-token" }
}
```

The id has two segments, `role/variant`, using lowercase letters, digits and hyphens. The path can also name `reviewer/config.json`. Referenced files must remain inside the pinned repository checkout. Secret bindings name references resolved by the server; never send credential values here.

Preview responds with `202` and an operation. Poll its `links.self` until it succeeds. Its `result` is the preview: `previewId`, `manifestDigest`, `sha`, the projected `definition`, `issues`, `permissions`, `secretsToBind`, `declaredSubAgents` and `expiresAt`. A failed validation has issues and no definition and cannot be installed. Preview fetches a cache but registers nothing and pins nothing.

An author can preview. An admin installs by posting to `/api/v1/agent-definitions` with an `Idempotency-Key` header:

```json
{
  "previewId": "<previewId>",
  "manifestDigest": "<manifestDigest>",
  "consent": {
    "granted": ["<every permission id from the preview>"],
    "declined": []
  }
}
```

Missing, unknown or declined permissions refuse the install. A source outside the configured trusted list adds a source permission. A changed source or expired preview requires a fresh preview. Installing returns `201` with the definition, which appears immediately in listing and capability matching. An existing local or installed id cannot be overwritten.

### Update, rollback and remove

Encode the id as one URL segment, for example `reviewer%2Fdefault`.

| Method | Path under `/api/v1`                     | Body or result                                                                                                      |
| ------ | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| POST   | `/agent-definitions/{id}/update/preview` | Optional `{ "ref": "v2", "secrets": { "SERVICE_TOKEN": "new-reference" } }`; operation whose result includes a diff |
| POST   | `/agent-definitions/{id}/update`         | The reviewed preview id, digest and consent                                                                         |
| GET    | `/agent-definitions/{id}/versions`       | Installed history, newest first, including the current version                                                      |
| POST   | `/agent-definitions/{id}/rollback`       | `{ "sha": "<full previously installed commit SHA>", "digest": "<optional exact version digest>" }`                  |
| DELETE | `/agent-definitions/{id}`                | Unregisters the agent; cached commits remain                                                                        |

An update preview can replace the secret-reference bindings when the newer config needs different variables. Updates retain unchanged permissions and ask again for added or changed requests. Rollback restores the selected version and its consent; it works from the cache when the remote is unavailable. A run that may still use an agent blocks update, rollback and removal until it finishes or is stopped. These lifecycle actions require an admin and record the actor in the control audit.

The generated SDK exposes these as `client.api.previewAgentDefinition`, `installAgentDefinition`, `previewAgentDefinitionUpdate`, `updateAgentDefinition`, `listAgentDefinitionVersions`, `rollbackAgentDefinition`, and `uninstallAgentDefinition`. Read the completed operation's result with the contract's `agentPreview` schema.

Downloaded peer lists are shown in preview; a run's graph determines the peers that are actually configured. Installed wrapper configs keep their detected backend. The consent recorded here does not yet implement per-tool permission enforcement or revocation during a run. The [Git library UI](./git-library.md) supports preview, consent, install, updates, version history, rollback and removal.
