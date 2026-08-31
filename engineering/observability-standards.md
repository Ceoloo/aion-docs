# Observability Standards

The engineering-facing companion to
[../architecture/observability.md](../architecture/observability.md). The
architecture defines *why* and *what*; this document defines *what code must
emit* to comply.

## The emission requirement

Every significant action emits the traceability spine. At minimum:

```
request_id, mission_id, workflow_id, run_id,
agent_id, actor_type, tool_used, model_used,
input_context_reference, decision, action, result,
latency, cost (tokens + spend), risk_level, approval_state,
outcome_reference, timestamp
```

Field meanings are defined in
[../architecture/observability.md](../architecture/observability.md).

## Rules for code

1. **Propagate the ID chain.** `request_id` → `mission_id` → `workflow_id` →
   `run_id` → `agent_id` flow through the whole call path. A new unit of work
   derives its IDs from its parent; it does not start a fresh, disconnected
   trace.
2. **Reference, don't copy.** Emit `input_context_reference` and
   `outcome_reference` — pointers — not the underlying (possibly sensitive)
   payloads.
3. **Emit on failure too.** A failed action emits the same spine with a
   `result` describing the failure. Failures are facts, not gaps.
4. **Always populate `risk_level`, `approval_state`, and cost.** No unclassified,
   ungated-status, unpriced action.
5. **`actor_type` and `model_used` are honest.** Record which runtime and model
   actually did the work, across interchangeable execution environments.
6. **Timestamps are consistent** (single clock source / UTC) so traces order
   correctly across services.

## Relationship to events

Observability traces and [events](event-standards.md) are related but distinct:

- **Events** are the durable business facts (`payment.received`).
- **Traces** are the operational record of *how* an action ran (latency, cost,
  which tool, approval state).

An action typically both emits events and produces a trace; the two are linked
by shared IDs (`run_id`, `outcome_reference`).

## Deferred tooling

The tracing/metrics/log backend is infrastructure, owned by `aion-infra` and
chosen via ADR. This standard governs the **shape of what is emitted**, which
must hold regardless of backend.

## Invariants

- **The ID chain is propagated end-to-end.**
- **Context and outcomes are referenced, not duplicated.**
- **Failures are emitted like successes.**
- **Risk, approval state, and cost are always present.**
