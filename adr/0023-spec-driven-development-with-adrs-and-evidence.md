# ADR-0023: Spec-driven development: specs, ADRs, a status file and recorded evidence

- Status: accepted
- Date: 2026-10-07
- Tasks: M0

## Context

The project is open source and will be maintained over a long time by several people and several AI agents. Intent and reasoning must survive between sessions and must be easy to turn into user documentation.

## Decision

Work is driven by written specs and a task tracker with stable requirement IDs. Each real choice is an ADR, written in the same change as the code. A status file states the active position and the next step. A task is complete only with a dated evidence entry (changes, commands and results, risks). Deferred work is filed as an issue in the repository that owns it. The instructions for people and agents start from one file in the planning repository. The ADRs and the evidence feed the user documentation.

## Consequences

- Any contributor or agent can resume work without chat history.
- Decisions can be audited and revised without rewriting history.
- Discipline cost on every task; a CI check that the index, statuses and evidence stay consistent would reduce the manual effort.
