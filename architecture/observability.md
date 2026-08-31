# Observability

**Every significant action in AION must be observable.** Observability is not a
dashboard added later — it is the trace that makes governance, debugging,
learning, and cost control possible.

This document defines the **standard**. It does not require the standard to be
fully implemented today; it requires that when actions are built, they are
traceable to it. Engineering-facing requirements are in
[../engineering/observability-standards.md](../engineering/observability-standards.md).

## The traceable action

A production AION action should eventually be traceable through this spine:

| Field | Meaning |
|---|---|
| `request_id` | The originating request. |
| `mission_id` | The mission this work serves. |
| `workflow_id` | The workflow being run. |
| `run_id` | This specific run/unit of work. |
| `agent_id` | The agent/worker identity, if any. |
| `actor_type` | native agent / external runtime / service / human. |
| `tool_used` | The tool invoked. |
| `model_used` | The model, if a model was involved. |
| `input_context_reference` | Reference to the assembled context (not a dump). |
| `decision` | What was decided. |
| `action` | What was done. |
| `result` | The result / status. |
| `latency` | Time taken. |
| `token/cost usage` | Tokens and cost consumed. |
| `risk_level` | The [risk level](../governance/risk-levels.md) of the action. |
| `approval_state` | Gate status: not required / pending / approved / rejected. |
| `outcome_reference` | Reference to the recorded outcome. |
| `timestamp` | When it happened. |

## Why each field earns its place

- **The `*_id` chain** (`request` → `mission` → `workflow` → `run` → `agent`)
  lets any action be located within the intent that caused it — essential for
  the [learning loop](learning-loop.md).
- **`actor_type` + `tool_used` + `model_used`** keep AION honest about *who or
  what* did the work, across interchangeable runtimes.
- **`input_context_reference` + `outcome_reference`** connect a decision to its
  inputs and its real result without copying data around.
- **`risk_level` + `approval_state`** make governance auditable: was a gate
  required, and what happened at it.
- **`latency` + `token/cost usage`** make cost and performance first-class,
  supporting the cost controls agents must declare.

## Requirements

1. **Traceability over completeness of tooling.** The IDs and references must be
   emitted even before rich dashboards exist.
2. **Context by reference, not by copy.** Observability records point at
   context and outcomes; they do not duplicate sensitive payloads.
3. **Failures are observed like successes.** A failed action carries the same
   spine; failure is a fact, not a gap.
4. **Cost and risk are always present.** No unpriced, unclassified action.

## Invariants

- **No significant action is untraceable.**
- **The trace spans layers** — one action is followed from intent through
  execution to outcome.
- **Observability is designed in, retrofitted never.**
