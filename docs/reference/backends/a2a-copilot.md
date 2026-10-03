# GitHub Copilot (`a2a-copilot`)

> Generated from the backend descriptor. Do not edit by hand; change `packages/agents/src/backends/a2a-copilot.json`.

GitHub Copilot via the Copilot SDK.

| | |
|---|---|
| Package | `a2a-copilot` (executable `a2a-copilot`) |
| Install | `npm i -g a2a-copilot` |
| Config section | `copilot` |
| Workspace key | `copilot.workspaceDirectory` |
| System prompt key | `copilot.systemPrompt` |
| Can orchestrate | no |
| Cost reporting | reported |
| Sideband events | yes |
| Resumable sessions | yes |
| Suggested models | `gpt-4.1`, `claude-sonnet-4.5`, `claude-opus-4.6` |

## Prerequisites

- **node**: Node.js 20 or newer. Install: `https://nodejs.org`
- **wrapper**: a2a-copilot is installed. Install: `npm i -g a2a-copilot`
- **gh** (optional): GitHub CLI authenticated (or GITHUB_TOKEN set). macOS: `brew install gh && gh auth login`. Windows: `winget install GitHub.cli, then gh auth login`. Linux: `https://cli.github.com then gh auth login`

Run `krama doctor` to check them on your machine.

## Environment

| Variable | Required | | Description |
|---|---|---|---|
| `GITHUB_TOKEN` | one of group `auth` | secret | Token with Copilot access. Needed when `gh` is not authenticated (headless, containers). |
| `COPILOT_MODEL` | no |  | Model override (same as options.model). |

Bind secrets with `backend.secrets` (variable name to secret reference). Values are never written to config files.

## Options

Set these under `backend.options` in an agent definition.

| Option | Type | Description |
|---|---|---|
| `cliUrl` | string | External Copilot CLI server URL (e.g. |
| `contextFile` | string | Filename for the pre-built domain context file in the workspace directory. |
| `contextPrompt` | string | Default prompt sent to build the domain context file. |
| `githubToken` | string | GitHub Personal Access Token for authentication. **(high risk)** **(secret)** |
| `model` | string | Default model for sessions (e.g. |
| `provider` | object | Custom LLM provider configuration (BYOK — Bring Your Own Key). |
| `streaming` | boolean | Enable streaming by default on sessions (default: true) |
| `systemPrompt` | string | System prompt prepended to the first message of every session. |
| `systemPromptMode` | `append`, `replace` | How the system prompt is applied to the SDK-managed system message. |
| `workspaceDirectory` | string | Working directory for context file operations |
