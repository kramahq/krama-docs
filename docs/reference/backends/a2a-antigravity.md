# Google Antigravity (`a2a-antigravity`)

> Generated from the backend descriptor. Do not edit by hand; change `packages/agents/src/backends/a2a-antigravity.json`.

Google Antigravity (Gemini) through a managed Python bridge.

| | |
|---|---|
| Package | `a2a-antigravity` (executable `a2a-antigravity`) |
| Install | `npm i -g a2a-antigravity` |
| Config section | `antigravity` |
| Workspace key | `antigravity.workingDirectory` |
| System prompt key | `antigravity.systemInstructions` |
| Can orchestrate | yes |
| Cost reporting | unknown |
| Sideband events | yes |
| Resumable sessions | yes |
| Suggested models | `gemini-3` |

## Prerequisites

- **node**: Node.js 20 or newer. Install: `https://nodejs.org`
- **wrapper**: a2a-antigravity is installed. Install: `npm i -g a2a-antigravity`
- **python**: Python 3.10 or newer (the wrapper manages its own virtualenv). macOS: `brew install python`. Windows: `winget install Python.Python.3.12`. Linux: `apt install python3 python3-venv`

Run `krama doctor` to check them on your machine.

## Environment

| Variable | Required | | Description |
|---|---|---|---|
| `GEMINI_API_KEY` | one of group `auth` | secret | Gemini API key (auth mode apiKey). |
| `GOOGLE_APPLICATION_CREDENTIALS` | one of group `auth` |  | Application Default Credentials file (auth mode adc). |

Bind secrets with `backend.secrets` (variable name to secret reference). Values are never written to config files.

## Options

Set these under `backend.options` in an agent definition.

| Option | Type | Description |
|---|---|---|
| `appDataDir` | string | SDK app data directory. |
| `bridgePath` | string | Override path to the private bridge.py. |
| `capabilities` | object | capabilities |
| `conversationId` | string | Existing SDK conversation id to resume. |
| `model` | string | Text model name. |
| `policies` | object | policies |
| `provider` | object | provider |
| `pythonPath` | string | Python executable used for the private bridge. |
| `responseSchema` | any | JSON schema object or path string for structured output. |
| `saveDir` | string | Persistent SDK save directory. |
| `skillsPaths` | string[] | Skill directory paths to pass to Antigravity. |
| `systemInstructions` | string | Appended system instructions. |
| `workingDirectory` | string | Convenience primary workspace. |
| `workspaces` | string[] | Workspace allowlist passed to LocalAgentConfig.workspaces. |
