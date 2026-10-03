# OpenCode (`a2a-opencode`)

> Generated from the backend descriptor. Do not edit by hand; change `packages/agents/src/backends/a2a-opencode.json`.

OpenCode through its local server API.

| | |
|---|---|
| Package | `a2a-opencode` (executable `a2a-opencode`) |
| Install | `npm i -g a2a-opencode` |
| Config section | `opencode` |
| Workspace key | `opencode.projectDirectory` |
| System prompt key | `opencode.systemPrompt` |
| Can orchestrate | yes |
| Cost reporting | unknown |
| Sideband events | yes |
| Resumable sessions | yes |
| Suggested models | any model the provider accepts |

## Prerequisites

- **node**: Node.js 20 or newer. Install: `https://nodejs.org`
- **wrapper**: a2a-opencode is installed. Install: `npm i -g a2a-opencode`
- **server**: An OpenCode server is running (opencode serve). Install: `Install OpenCode (opencode.ai) and run `opencode serve`.`

Run `krama doctor` to check them on your machine.

## Environment

| Variable | Required | | Description |
|---|---|---|---|
| `OPENCODE_URL` | no |  | URL of the running OpenCode server (default http://localhost:4096). |
| `OPENCODE_MODEL` | no |  | Model as provider/model. |
| `OPENCODE_AGENT` | no |  | OpenCode agent preset. |

Bind secrets with `backend.secrets` (variable name to secret reference). Values are never written to config files.

## Options

Set these under `backend.options` in an agent definition.

| Option | Type | Description |
|---|---|---|
| `agent` | string | Default agent (e.g. |
| `baseUrl` | string | OpenCode server base URL (default: "http://localhost:4096") |
| `contextFile` | string | Filename for the pre-built domain context file in the workspace directory. |
| `contextPrompt` | string | Default prompt sent to OpenCode to build the domain context file. |
| `model` | string | Default model (e.g. |
| `projectDirectory` | string | Target project directory for all OpenCode API calls |
| `systemPrompt` | string | System prompt prepended to the first message of every session. |
| `systemPromptMode` | `append`, `replace` | How the system prompt is applied when injected into the first user message. |
