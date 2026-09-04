# Agent Trace Schema (OpenTelemetry-Compatible)

The [observability standard](observability-standards.md) defines *what every
action must emit*. This document defines how that spine maps onto
**OpenTelemetry (OTel)** semantics, so AION traces interoperate with the wider
agent-infrastructure ecosystem — LangGraph, the OpenAI SDK, agent frameworks,
and custom runtimes can land in one trace architecture — without AION coupling to
any single vendor's backend.

It is motivated by the
[2026 Runtime-Control Brief](../research/2026-runtime-control-brief.md) (signal
3): the field is standardizing agent telemetry on OTel, and AION's traces should
speak that language. This governs the **shape and mapping** of what is emitted;
the collector/exporter backend is infrastructure, owned by `aion-infra` and
chosen by ADR ([observability-standards](observability-standards.md) — deferred
tooling). See [`aion-infra/docs/design/agent-trace-pipeline.md`].

## Principle

AION's telemetry spine is **already the right data**; OTel is a **serialization
and propagation convention**, not a redesign. We map, we do not rebuild. AION's
`TelemetryRecord` remains the canonical emission; the OTel mapping is how it
crosses process and vendor boundaries.

## Span model

One AION **run** is a trace; each lifecycle step is a **span**:

```
trace  = run_id
 ├─ span: command.received
 ├─ span: policy.evaluate        (decision, risk_level, autonomy tier)
 ├─ span: approval.gate          (approval_state)   [if gated]
 ├─ span: execution.dispatch     (executor, model, tool_used)
 │    └─ span: tool.call         (arguments_hash, cost)   [child per tool]
 └─ span: execution.completed | execution.failed
```

- **Trace/span IDs** are derived from AION's ID chain so a trace is navigable in
  both AION and any OTel backend. `run_id` anchors the trace; child work derives
  its span context from its parent — never a fresh, disconnected trace
  ([observability-standards](observability-standards.md) rule #1).
- **Handoffs** between execution environments (native → external runtime →
  human) are spans with the same trace context, so an agent-to-agent or
  agent-to-human handoff stays in one trace.

## Attribute mapping

AION spine field → OTel span attribute. AION-specific attributes use the `aion.`
namespace; where an OTel GenAI semantic-convention attribute exists, emit both
(the standard name for interoperability, the `aion.` name for exactness).

| AION field | OTel attribute | Notes |
|---|---|---|
| `request_id` | `aion.request_id` | trace-level baggage |
| `mission_id` | `aion.mission_id` | baggage; propagated |
| `workflow_id` | `aion.workflow_id` | baggage |
| `run_id` | `aion.run_id` / trace id | the trace anchor |
| `agent_id` | `aion.agent_id` | attributable identity |
| `actor_type` | `aion.actor_type` | native/external/service/human |
| `tool_used` | `gen_ai.tool.name` + `aion.tool_id` | tool span |
| `model` | `gen_ai.request.model` + `aion.model` | vendor-neutral, honest |
| `tokenUsage` | `gen_ai.usage.total_tokens` | when a model was involved |
| `cost` | `aion.cost` | abstract units (see economics) |
| `decision` | `aion.policy.decision` | ALLOW/DENY/REQUIRE_APPROVAL |
| `risk_level` | `aion.risk_level` | always present |
| `approval_state` | `aion.approval_state` | always present |
| autonomy tier | `aion.autonomy_tier` | see [autonomy-tiers](../governance/autonomy-tiers.md) |
| `arguments_hash` | `aion.arguments_hash` | on tool/execution spans (ADR-003) |
| `input_context_reference` | `aion.context_ref` | **reference, never the payload** |
| `outcome_reference` | `aion.outcome_ref` | reference |
| span status | `otel.status_code` | `failed` → `ERROR`; failures are spans, not gaps |

## Non-negotiable carries

The OTel mapping does not get to drop AION's invariants:

1. **Reference, don't copy.** Context and outcomes are emitted as references
   (`aion.context_ref`, `aion.outcome_ref`), never as span payloads. Sensitive
   data does not enter spans.
2. **Failures are spans.** A failed action emits a span with `ERROR` status and
   the same spine — failure is a fact, not a missing span.
3. **Risk, approval state, and cost are always present** on the relevant spans.
4. **`actor_type` and `model` are honest** — the mapping records which runtime
   and model actually did the work, across interchangeable environments.
5. **The ID chain propagates as baggage** end-to-end, including across handoffs
   to external runtimes and humans.

## Boundaries

- **This is a mapping standard, not a backend choice.** No exporter, collector,
  sampling policy, or storage engine is specified here; those are `aion-infra`
  concerns chosen by ADR when a mission needs them.
- **AION's `TelemetryRecord` stays canonical.** If OTel semantic conventions
  evolve, the mapping table changes; the emitted AION spine does not churn.
- **Trace vs. event vs. receipt** remain distinct
  ([observability-standards](observability-standards.md),
  [event-standards](event-standards.md),
  [execution-gateway](../architecture/execution-gateway.md)); the trace is the
  *operational* record, linked to events, receipts, and outcomes by shared IDs.

## Invariants

- **AION emits its canonical spine; OTel is the interoperable projection of it.**
- **The ID chain maps to trace/span context and propagates across every
  handoff.**
- **References, not payloads, cross the span boundary.**
- **Failures are ERROR spans; risk, approval, and cost are always present.**
