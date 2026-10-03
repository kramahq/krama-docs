# Adding a backend

A **backend** is one coding agent that Krama can run, reached through an [A2A](https://a2a-protocol.org) wrapper such as `a2a-claude`, `a2a-codex`, `a2a-copilot`, `a2a-opencode` or `a2a-antigravity`. Krama ships descriptors for these five. When another provider appears, you add one JSON file. You do not change Krama's code.

## Why a descriptor

Every wrapper shares one core, so most settings are identical everywhere: the agent card, the server, sessions, timeouts, logging, MCP servers, memory and sub-agents. Each wrapper then adds its own things:

| Common to all | Different per provider |
|---|---|
| `--config`, `--port`, `--hostname`, `--advertise-host`, `--log-level` | The provider section of the config (`claude`, `codex`, `copilot`, `opencode`, `antigravity`) |
| Agent card at `/.well-known/agent-card.json` | Where the **workspace directory** goes (`workingDirectory`, `workspaceDirectory`, `projectDirectory`) |
| Shared sections: `session`, `features`, `timeouts`, `logging`, `mcp`, `memory`, `subAgents`, `events` | Where the **system prompt** goes (`systemPromptAppend`, `developerInstructions`, `systemPrompt`, `systemInstructions`) |
| A `model` setting | Credentials (`ANTHROPIC_API_KEY`, `OPENAI_API_KEY`, `GITHUB_TOKEN`, `GEMINI_API_KEY`, …) |
| | Prerequisites (Python for Antigravity, a running `opencode serve`, `gh` or a token for Copilot) |
| | Provider options (sandbox mode, permission mode, budgets, plugins, …) |

A descriptor records both halves. Krama uses it to validate an agent's settings, translate common fields into the provider's own keys, build the launch command, check that your machine is ready, and show the right form in the UI.

## Add a provider in four steps

### 1. Install the wrapper

```bash
npm i -g a2a-foo
a2a-foo --version
```

### 2. Scaffold the descriptor from the wrapper's schema

Every wrapper ships `schemas/agent-config.schema.json`. Krama reads the provider section of it, so the option list is exact:

```bash
node packages/agents/scripts/scaffold-backend.ts \
  --schema "$(npm root -g)/a2a-foo/schemas/agent-config.schema.json" \
  --id a2a-foo --package a2a-foo --provider foo \
  --out ~/.krama/backends/a2a-foo.json
```

The command guesses the workspace and system prompt keys, produces a descriptor that already validates, and lists what is left to fill in.

### 3. Fill in what a schema cannot tell us

Open the file and complete:

- **`env`**: the environment variables the wrapper reads. Mark API keys `"secret": true`. Variables that share a `"group"` are alternatives: any one satisfies the group.
- **`prerequisites`**: everything besides the wrapper, such as a CLI, a running service, or a runtime. Give a `check` (a command, a URL, or `{ "manual": true }`) and install hints per OS.
- **`capabilities`**: `canOrchestrate` is true only if the backend can reliably call MCP tools. `cost` is `reported` only if the wrapper sends provider-reported cost (otherwise `unknown` or `not_reported`; Krama never estimates). Also `sideband` and `resumableSessions`.
- **`models`**: suggestions for pickers. Any model the provider accepts still works.
- **`options[].risk`**: set `"high"` on switches that widen what the agent may do (bypassing permissions, network access, full-disk sandboxes). The consent screen flags them.

### 4. Check it

```bash
krama backend validate ~/.krama/backends/a2a-foo.json   # schema and consistency
krama doctor                                            # prerequisites on this machine
```

Your editor can validate and autocomplete the file if it points at the schema:

```json
{ "$schema": "https://raw.githubusercontent.com/kramahq/krama/main/packages/contract/schemas/backend-descriptor.schema.json" }
```

## Where descriptors live

| Source | Location | Notes |
|---|---|---|
| Built-in | Shipped with Krama | The five above |
| User | `<KRAMA_HOME>/backends/*.json` | Add or replace a built-in; a replaced id is reported |
| Pack | `backends/` inside a pack | Installing it asks for consent, because a descriptor decides what runs |

An invalid file is reported with the file name and reason; the other files still load.

> **Status.** The registry, validation, launch builder, doctor and scaffold command are implemented in `@kramahq/agents`. Loading `<KRAMA_HOME>/backends` at server start and the `krama backend validate` / `krama doctor` commands arrive with the server (M5.1) and CLI (M7.1) milestones. Until then use the scaffold command and `BackendRegistry.loadDir`.

## Using a backend in an agent definition

The agent definition names the backend and supplies provider options, shared settings and secret bindings:

```yaml
backend:
  wrapper: a2a-codex
  model: gpt-5-codex
  options:               # provider-specific, validated against the descriptor
    sandboxMode: workspace-write
    networkAccessEnabled: false
  common:                # shared by every wrapper
    timeouts: { delegationMs: 1800000 }
  secrets:               # environment variable -> secret reference
    OPENAI_API_KEY: openai-main
```

Krama then writes the wrapper's config (workspace, model and system prompt in that provider's own keys), starts the wrapper on a free local port bound to loopback, and passes only the environment variables the descriptor declares. Secret values never go into the config file, logs or events. A literal secret in `options` is rejected; `${ENV_VAR}` references are allowed.

## Descriptor reference

| Field | Meaning |
|---|---|
| `id` | The backend id, used as `backend.wrapper`. Lowercase letters, digits, dashes. |
| `package` | npm package, executable name, install command, optional minimum version |
| `launch` | Informational default port, readiness path, startup timeout, extra args. Krama allocates real ports dynamically. |
| `providerKey` | Name of the provider section in the wrapper config |
| `mapping` | Where Krama's common settings land inside the provider section: `workspace`, `model`, `systemPrompt`, `allowedTools` |
| `options` | Provider options: `key`, `type`, `values` (enums), `description`, `default`, `required`, `env`, `risk`, `secret` |
| `env` | Environment variables: `name`, `description`, `required`, `secret`, `group` |
| `prerequisites` | Checks for `krama doctor`: `kind`, `check`, `optional`, `install` per OS |
| `capabilities` | `canOrchestrate`, `cost`, `sideband`, `resumableSessions` |
| `models` | Suggested model names |

See the generated pages for each built-in backend: [Backends](../reference/backends/index.md).

## Keeping descriptors current

Re-run the scaffold command against a newer wrapper release and compare the `options` list. The repository's tests do this automatically for the built-ins when `A2A_WRAPPER_DIR` points at a checkout of `a2a-wrapper`, and fail if an option was added or removed.

Some behaviour belongs to the wrapper, not to Krama: reporting cost for every provider, structured permission requests for paths outside the workspace, and Windows support. Those are tracked as wrapper tasks and change a descriptor's `capabilities` as they land.
