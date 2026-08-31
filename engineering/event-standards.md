# Event Standards

Events are the backbone of AION's memory and learning. This standard makes them
trustworthy. It is the enforcement of [Principle 5](principles.md): **events
represent what happened.**

## The core rule

**An event is a completed fact, stated in the past tense. A command or intention
must never be disguised as an event.**

| Good — facts | Avoid — commands |
|---|---|
| `lead.created` | `send.proposal` |
| `call.completed` | `do.outreach` |
| `proposal.sent` | `create.lead` |
| `payment.received` | `start.deployment` |
| `deployment.failed` | |

If a name reads as an instruction ("send", "do", "create", "start"), it is a
command, not an event. Commands belong to the orchestration/API surface, not the
event log.

## Naming

- **`entity.pastTenseVerb`** — the subject that something happened to, then what
  happened. `payment.received`, `deployment.failed`.
- **Domain-scoped and stable.** Names are part of a contract; renaming an event
  is a breaking change.
- **Specific over generic.** `proposal.sent` beats `notification.sent` when the
  fact is specifically about a proposal.

## Structure

Each event carries, at minimum:

- **identity**: event id, event name, occurred-at timestamp;
- **subject**: the canonical entity it concerns (owned by `aion-data`);
- **payload**: the facts of what happened (not intentions);
- **lineage/trace**: the IDs linking it to the run that produced it
  (`run_id`, etc.) per [observability](observability-standards.md).

## Rules

1. **Append-only.** Events are immutable facts. Never edited or deleted to
   rewrite history; corrections are new events. See
   [../architecture/data-layer.md](../architecture/data-layer.md).
2. **Past tense, completed.** An event is emitted *after* the fact occurs, not to
   request it.
3. **Owned subjects.** An event's subject entity has a canonical owner; events
   do not invent competing entity definitions.
4. **Facts, not intentions.** The payload records what is true, not what someone
   wants to happen.
5. **Contract-stable.** Event names and shapes are versioned like APIs; changes
   that matter architecturally are [ADR](../adr/README.md)-backed.

## Why this is strict

The [learning loop](../architecture/learning-loop.md) reconstructs outcomes from
events. If commands masquerade as events, the log records wishes instead of
reality, and every lesson derived from it is corrupt. Honest events are
non-negotiable.

## Deferred tooling

The event bus/broker is chosen when throughput/reliability requirements justify
it (via ADR) — see the deferred-technology guidance in
[../architecture/data-layer.md](../architecture/data-layer.md). This standard
governs event **semantics**, which hold regardless of transport.

## Invariants

- **Events are past-tense, completed facts.**
- **Commands are never disguised as events.**
- **Append-only; corrections are new events.**
- **Subjects are canonically owned; names are contract-stable.**
