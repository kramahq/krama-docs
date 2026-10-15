# ADR-0019: Agents and packs are two layers: reusable agent definitions, and packs that wire them

- Status: accepted
- Date: 2026-10-07
- Tasks: M3.4, M4.8, M6.1, M9.1

## Context

Defining an agent (backend, model, instructions, tools, skills) is a different job from deciding how a team of agents works. The same reviewer or researcher agent is useful in many packs. Definitions should be maintainable by many people in git, with some provided out of the box.

## Decision

Layer one is the **agent**: a wrapper configuration directory (ADR-0007), kept in a git repository that people maintain, with a set provided by the project. Layer two is the **pack**: it picks an orchestrator and agents from those definitions (from git, embedded, or external) and wires them with hints (ADR-0006). Krama starts agents from their definitions and manages the pool of processes and ports.

## Consequences

- Agents are reused across packs and can be improved independently.
- A pack pins the versions of the agents it uses (ADR-0015), so a change in a shared agent does not silently change an old pack.
- Two places to look when something behaves unexpectedly; the Studio and the run view show the resolved result.


## Implementation (2026-10-16): installing reusable agents

The instance agent pool accepts a Git repository, ref and configuration directory (or its `config.json`), an instance-local `role/variant` id, and secret references. A preview detects the backend using its registered descriptor, validates the wrapper configuration and referenced files, and presents tools, secret references, declared peers and trust. An admin installs the reviewed digest after answering its permission requests. Registration starts no process.

The pool keeps each reviewed commit and its digest, consent, actor and history on disk. Its immutable-commit lock entries do not move a pack's independently pinned refs. Updates have a new preview and diff; new or changed permission requests need consent. Rollback restores a previously installed version and its consent without following a moving ref. The cached catalogue and configurations survive a restart without the remote.

The public definition is a projection of the wrapper configuration. Runs receive the original configuration with instruction and skill paths resolved against its pinned checkout; the source is never edited. The run's graph replaces declared peers, and Krama assigns event reporting. An installed configuration uses its detected backend; a model override stays within that backend.

Updates, rollback and uninstall are refused while an unfinished run may use the definition, including roster selections that happen when a worker starts. This keeps the current implementation from changing an agent beneath an active run. Per-run definition snapshots may remove that restriction later.

The registry is published to in-memory readers after its audit append succeeds. A failed append restores the previous registry file. This is filesystem persistence plus an audit append, with no database transaction across both stores.

The pool is available through the agent-definition API and generated SDK. Runtime permission enforcement, the pool UI and Studio authoring are separate work.
