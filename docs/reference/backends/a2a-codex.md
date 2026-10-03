# OpenAI Codex (`a2a-codex`)

> Generated from the backend descriptor. Do not edit by hand; change `packages/agents/src/backends/a2a-codex.json`.

OpenAI Codex via the Codex SDK.

| | |
|---|---|
| Package | `a2a-codex` (executable `a2a-codex`) |
| Install | `npm i -g a2a-codex` |
| Config section | `codex` |
| Workspace key | `codex.workingDirectory` |
| System prompt key | `codex.developerInstructions` |
| Can orchestrate | yes |
| Cost reporting | unknown |
| Sideband events | yes |
| Resumable sessions | yes |
| Suggested models | `gpt-5-codex`, `o4-mini` |

## Prerequisites

- **node**: Node.js 20 or newer. Install: `https://nodejs.org`
- **wrapper**: a2a-codex is installed. Install: `npm i -g a2a-codex`
- **git**: The workspace is a Git repository (or set options.skipGitRepoCheck). Install: `https://git-scm.com/downloads`
- **auth**: OPENAI_API_KEY is available to the agent

Run `krama doctor` to check them on your machine.

## Environment

| Variable | Required | | Description |
|---|---|---|---|
| `OPENAI_API_KEY` | yes | secret | OpenAI API key for the Codex SDK. |

Bind secrets with `backend.secrets` (variable name to secret reference). Values are never written to config files.

## Options

Set these under `backend.options` in an agent definition.

| Option | Type | Description |
|---|---|---|
| `additionalDirectories` | string[] | Additional directories to make accessible to Codex beyond workingDirectory. |
| `approvalPolicy` | `never`, `on-request`, `on-failure`, `untrusted` | Tool approval policy for shell commands and MCP tool calls. **(high risk)** Default: `"never"`. |
| `baseUrl` | string | Override the OpenAI API base URL (useful for corporate proxies). |
| `codexPathOverride` | string | Override the path to the Codex CLI binary. |
| `configOverrides` | object | Additional Codex configuration overrides passed as CodexOptions.config. |
| `contextFile` | string | Filename for the pre-built domain context file within workingDirectory. Default: `"context.md"`. |
| `contextPrompt` | string | Default prompt used when buildContext() is called without an explicit prompt. |
| `developerInstructions` | string | Instructions prepended to every Codex prompt as developer context. |
| `model` | string | Model to use (e.g. |
| `networkAccessEnabled` | boolean | Allow Codex to make outbound network requests. **(high risk)** Default: `false`. |
| `sandboxMode` | `read-only`, `workspace-write`, `danger-full-access` | Codex sandbox mode controlling filesystem access. **(high risk)** Default: `"workspace-write"`. |
| `skipGitRepoCheck` | boolean | Skip Codex's built-in Git repository validation check. Default: `false`. |
| `webSearchMode` | `disabled`, `cached`, `live` | Web search access for Codex. Default: `"disabled"`. |
| `workingDirectory` | string | Absolute path to the Git repository Codex should operate on. |
