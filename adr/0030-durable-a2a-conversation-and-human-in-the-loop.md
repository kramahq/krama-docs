# ADR-0030: Durable A2A conversations and human-in-the-loop handling

- Status: proposed
- Date: 2026-10-07
- Tasks: M3.7, M4.10

## Context

Agents are remote processes that can fail, stream, stop to ask a question, or ask for authorisation. A client that treats a send as an in-memory call loses work when it crashes and cannot tell whether an agent received a message. People must be able to answer questions and approve risky actions safely, and the record must show exactly what was approved.

## Decision

**Conversation.** A send is a command with an idempotency key and a stable A2A `messageId`, dispatched once. A failure after the request left is recorded as *uncertain* and is never resent automatically; a failure before anything was sent is retried with backoff. Responses, streams, push callbacks and polling all pass through one ingestion path that de-duplicates, captures the raw event for the transcript (ADR-0013) and updates task state. After a drop Krama re-attaches with `SubscribeToTask`, which observes and never resends, and reconciles open tasks with `GetTask`; a late read never reopens a terminal task. Remote task ids are keyed with the agent and never passed to another agent.

**Human in the loop.** Three different situations are kept apart: *input required* (a question), *auth required* (resolved out of band and never treated as an approval) and an *approval*. An approval is a decision on an immutable, typed proposed action with a digest; the reviewer names the revision they read and a newer one makes the decision fail; outcomes are approve, reject, edit (a new revision), request changes and delegate, with a rationale except for approve. Authority belongs to Krama (role, grant, assignee, separation of duties), never to the agent; an agent may propose an approval only through an advertised extension. The decision, the execution record and the command to the agent commit together, keyed by the decision so a replay cannot dispatch twice, and the approved message carries the approval identity so a cooperating agent can check it.

## Consequences

- Work survives restarts and the record shows what was approved and what was sent.
- More machinery than an in-memory call: a command store, a worker or sweep, and decision revisions.
- The current `request_decision` model has no immutable proposed action, so it must change (M4.10).
- Push callbacks are optional; Krama starts its own agents, so subscription plus reconciliation may be enough.

## Implementation notes (M3.7, 2026-10-09)

The conversation half is built; the human-in-the-loop half is still M4.10, so the status stays `proposed`.

- **Where the send is recorded.** On the step (`a2a.messageId`, `a2a.delivery`), not in a separate command table. The step is written with `pending` before anything is sent, so a crash leaves a record that says the message may have left. A restart reads `pending` (or a missing task id) as *uncertain* and never sends it again. This removes the need for an outbox and a worker for now; a separate command store can come with M4.10 if decisions need it.
- **Before or after.** The gateway marks an error `dispatched: false` only when it failed before the request was made (reading the card, address and credential checks). Everything else, including an error that does not say, counts as possibly delivered. Only an unreachable address is retried with backoff, under the same `messageId`.
- **After a dropped stream.** If the agent had already answered, the engine follows the task with `SubscribeToTask` (up to three times) and then reads it with `GetTask`. Artifacts the agent repeats are stored once (by name and content hash). A state event for a step that has already finished is ignored and noted on the transcript.
- **After a restart.** `failOrphaned` asks the agent what it holds: a finished task is taken over, a running one is canceled (so it does not spend unattended) and the step is failed so a person can choose to retry it.
- **Not built.** A periodic `GetTask` sweep of open tasks while the server runs (reconciliation happens after a drop and after a restart, not on a timer), `ListTasks` sweeps, and push callbacks.
