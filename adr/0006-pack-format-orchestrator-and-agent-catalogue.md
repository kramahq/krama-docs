# ADR-0006: A pack is one YAML: an orchestrator and a catalogue of agents wired by hints

- Status: accepted
- Date: 2026-10-03
- Tasks: M4.5, M4.8, M4.9, M6.1

## Context

A pack defines how a use case is done by several agents. Earlier ideas (a fixed roster, methodology phases and gates written into the pack) baked one working style into the engine and made packs hard to author.

## Decision

A pack is one self-contained `krama-pack.yaml` that names **one orchestrator** and a **catalogue of agents** keyed by unique id. An agent comes from `config` (an embedded wrapper config), `git` (a wrapper config directory at a pinned version) or `external` (a remote A2A agent). Wiring is `subAgents: [{agent, hint}]`, where the hint tells the caller when to use that agent. The orchestrator decides and records the plan; phases appear as the orchestrator plans them. Any agent can be the orchestrator on any backend that supports it.

## Consequences

- Authoring a pack is mostly listing agents and describing how they relate.
- The engine carries no methodology; process lives in prompts and hints.
- The engine still reads the older methodology and roster until they are removed (M4.9).

## Run controls confirmed — 2026-10-11

Removing the methodology must preserve deterministic run controls. The agreed replacement is:

- The orchestrator records its plan through `set_plan`. A run without an explicit plan uses one implicit phase.
- The engine limits revision loops using platform policy.
- Review mode requires a human decision before successful completion. Autopilot omits this final review; other required decisions still block progress.

These are requirements for M4.9, which is not yet implemented. The current UI must describe existing pack reviews and requested decisions accurately until the engine enforces the final review rule. A configured backend or budget is not evidence that an agent was launched or that a native worker call passed a pre-dispatch check.

## Implementation notes (M4.9, 2026-10-13)

The run controls above are built; the older methodology and roster are still read, and are deprecated.

- **A pack without a methodology** starts a run with one implicit phase, `run`. The orchestrator replaces it by calling `set_plan` with phase ids, labels and dependencies, and may call it again to revise phases that have not started. A started or finished phase stays in the plan with the same dependencies, so a revision cannot rewrite history. A pack that still declares its own phases refuses `set_plan`.
- **Loop caps** come from platform policy (`defaultMaxLoops`). A `loop_back` beyond the cap blocks the run with a reason, so a person decides.
- **Final approval.** In `review` mode, a run of a pack with no methodology raises one `approval` decision, "Final approval", when every phase is done, and waits. Approving completes the run; rejecting stops it and keeps the artifacts. `autopilot` skips it. The engine has one place where a run completes, and that place applies the rule, so no path (a late phase outcome, a re-plan, a decision) completes a review-mode run without it. A re-plan clears an approval already given, because there may be new work to approve. Packs that keep a methodology are unchanged: their gates are their approvals.
- **Relay delegation over a graph.** With `krama` delegation the orchestrator delegates to the agents it references directly, by agent id. Krama starts each one on first use from its own config, and it calls nobody. Deeper wiring and external agents still need `native` delegation.
- **Not built:** a "request changes" outcome on the final approval (reject is the only way back), per-pack overrides of the loop cap, and removing `methodology` and `roster` from the contract (that waits for the SDLC pack port).
