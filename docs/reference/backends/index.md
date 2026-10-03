# Backends

> Generated from the built-in backend descriptors.

Krama talks to every coding agent through an [A2A](https://a2a-protocol.org) wrapper. Each wrapper shares one core and adds its own provider section, credentials and prerequisites.

| Backend | Package | Can orchestrate | Cost reporting |
|---|---|---|---|
| [Google Antigravity](./a2a-antigravity.md) | `a2a-antigravity` | yes | unknown |
| [Claude](./a2a-claude.md) | `a2a-claude` | yes | unknown |
| [OpenAI Codex](./a2a-codex.md) | `a2a-codex` | yes | unknown |
| [GitHub Copilot](./a2a-copilot.md) | `a2a-copilot` | no | reported |
| [OpenCode](./a2a-opencode.md) | `a2a-opencode` | yes | unknown |

Need another provider? See [Adding a backend](../../guides/adding-a-backend.md).
