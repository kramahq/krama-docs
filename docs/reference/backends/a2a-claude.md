# Claude (`a2a-claude`)

> Generated from the backend descriptor. Do not edit by hand; change `packages/agents/src/backends/a2a-claude.json`.

Anthropic Claude via the Claude Agent SDK.

| | |
|---|---|
| Package | `a2a-claude` (executable `a2a-claude`) |
| Install | `npm i -g a2a-claude` |
| Config section | `claude` |
| Workspace key | `claude.workingDirectory` |
| System prompt key | `claude.systemPromptAppend` |
| Can orchestrate | yes |
| Cost reporting | unknown |
| Sideband events | yes |
| Resumable sessions | yes |
| Suggested models | `claude-opus-5-5`, `claude-sonnet-5-5` |

## Prerequisites

- **node**: Node.js 20 or newer. Install: `https://nodejs.org`
- **wrapper**: a2a-claude is installed. Install: `npm i -g a2a-claude`
- **auth**: ANTHROPIC_API_KEY is available to the agent. Install: `Create a key at console.anthropic.com and bind it as a secret.`

Run `krama doctor` to check them on your machine.

## Environment

| Variable | Required | | Description |
|---|---|---|---|
| `ANTHROPIC_API_KEY` | yes | secret | Anthropic API key for the Claude Agent SDK. |
| `CLAUDE_MODEL` | no |  | Model override (same as options.model). |
| `CLAUDE_EFFORT` | no |  | Reasoning effort override. |

Bind secrets with `backend.secrets` (variable name to secret reference). Values are never written to config files.

## Options

Set these under `backend.options` in an agent definition.

| Option | Type | Description |
|---|---|---|
| `additionalDirectories` | string[] | Additional directories Claude can access. |
| `allowedTools` | string[] | Tools auto-allowed without prompting. |
| `contextFile` | string | Filename for the pre-built domain context file within workingDirectory. Default: `"context.md"`. |
| `contextPrompt` | string | Default prompt used when buildContext() is called without an explicit prompt. |
| `customSystemPrompt` | string | Full system prompt replacement. |
| `dangerouslyAllowBypassPermissions` | boolean | Must be true when permissionMode is "bypassPermissions". **(high risk)** |
| `disallowedTools` | string[] | Tools removed from the model's context entirely. |
| `effort` | `low`, `medium`, `high`, `xhigh`, `max` | Reasoning effort level. |
| `enabledPlugins` | object | Plugins to enable, keyed `"<plugin-id>@<marketplace-id>"`, where the marketplace id must appear in `marketplaces`. |
| `executablePathOverride` | string | Override the path to the Claude executable. |
| `fallbackModel` | string | Fallback model when the primary is overloaded/unavailable. |
| `marketplaces` | object | Plugin marketplaces to register for the session, keyed by marketplace id. |
| `maxBudgetUsd` | number | Max budget in USD per query. |
| `maxTurns` | number | Max conversation turns per query (runaway protection). |
| `model` | string | Model (e.g. |
| `permissionMode` | `acceptEdits`, `dontAsk`, `plan`, `bypassPermissions` | Permission mode. **(high risk)** |
| `sandbox` | object | Opaque SDK sandbox settings passthrough (OS-level command sandboxing). |
| `settingSources` | string[] | Filesystem settings sources to load. |
| `systemPromptAppend` | string | Appended to the claude_code preset system prompt (developerInstructions analog). |
| `thinking` | any | Extended thinking behavior. |
| `workingDirectory` | string | Absolute path to the workspace Claude operates on. |
