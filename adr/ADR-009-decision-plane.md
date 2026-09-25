# ADR-009: Decision Plane (System-One Judgments)

- **Status:** Accepted
- **Date:** 2026-09-18
- **Decision Owners:** Runtime / AI Engineer

## Context

AION runs a large generative model for many small, repeated decisions: which
agent handles a request, which pipeline stage a lead is in, whether an action
matches mission intent, whether a log line needs attention. The pattern is
`state → reasoning → tokens → text/JSON → parse → validate → action` — expensive
and slow when done thousands or millions of times, and structurally fragile
(the model can return an answer outside the expected set).

TypeSafe's **Jev** (introduced Sep 14, 2026) popularized a "System One" model
category: constrained, typed judgments (`binary` / `choice` / `score`) returned
with a probability distribution, at ~70–500 ms and a tiny fraction of LLM cost.
The value for AION is not the vendor or the headline benchmark multipliers — it
is the *architecture*: separate cheap, calibrated **judgment** from expensive
**generation** and from deterministic **governance**.

Two cautions shaped this decision:

- "Zero hallucinations" is only structural: a constrained provider cannot emit
  an out-of-space value, but a **valid output is not a correct decision**.
- TypeSafe's own terms say output may be inaccurate and must be independently
  evaluated. So confidence is a **signal, not permission**.

## Decision

1. **We will add an AION Decision Plane as a vendor-neutral package,
   `@aion/decision-engine` (in `aion-action-engine/packages/`).** It provides a
   `DecisionProvider` seam and turns state + typed questions into typed,
   validated decisions with probabilities.

2. **Adopt the architecture, not the vendor.** TypeSafe Jev, an LLM-as-judge,
   and a deterministic rules engine all implement one `DecisionProvider`
   interface. The first release ships `RulesProvider` (deterministic baseline),
   `LLMDecisionProvider` (System-Two judge seam), and `TypeSafeJevProvider`
   (System-One; **inert until credentials are supplied**, with a pure,
   unit-tested response mapper).

3. **The output space is enforced.** Every result is validated against its
   question's declared space (`assertResultValid`); an out-of-space answer is
   rejected, not passed downstream. This is the structural guarantee — and the
   limit of it.

4. **A confidence + risk policy engine routes decisions**, it does not execute
   them: `auto_execute` / `auto_execute_low_risk` / `llm_verify` / `reasoning` /
   `human_approval`. Risk uses the AION governance taxonomy (R0–R3); a high-risk
   action always routes to a human regardless of confidence. Thresholds are
   defaults meant to be **replaced by the tenant's own calibration data**.

5. **The Decision Plane never authorizes side effects.** Routing to
   `auto_execute` is a recommendation; the AION policy engine and Execution
   Gateway (ADR-003, and the authority/provenance primitives) remain the sole
   authority for what is allowed to happen. Confidence is never authority.

6. **Every decision is recorded** (`DecisionRecord` + `hashState`) and a
   **shadow-mode evaluator** computes accuracy per confidence bucket, expected
   calibration error, human-disagreement and false-automation rates, and
   cost/latency — so thresholds are earned from evidence. First rollout is
   **shadow mode on Revenue Copilot / GHL**: record decisions, change nothing.

## Alternatives Considered

- **Keep using an LLM for every judgment** — Rejected on cost, latency, and the
  lack of a constrained, measurable output space.
- **Hard-code `jev.decide()` at call sites** — Rejected; marries AION to one
  vendor. The `DecisionProvider` seam keeps providers swappable/cascadable.
- **Let a high-confidence decision execute directly** — Rejected; collapses the
  judgment/governance separation. Execution stays behind the policy engine and
  Execution Gateway.
- **Put the package in `aion-core`** — Deferred; `aion-core` is a single-package
  control-plane library. The Decision Plane is a distinct plane that *feeds* the
  gateway, and `aion-action-engine`'s workspace already hosts sibling packages
  (`scoring`, `permissions`, `events`).

## Consequences

### Positive

- Large share of routine judgments can move off generative models onto cheap,
  fast, typed calls — measurably, via the ledger.
- A reliability layer (calibration) becomes a first-class asset and a moat:
  AION can state its intelligence's measured accuracy, not a model name.
- Governance philosophy is intact: judgment informs; policy + gateway decide.

### Negative

- Another moving piece and provider surface to operate and monitor.
- Calibration requires enough shadow volume before thresholds can be trusted;
  premature "go live" would trade reasoning cost for decision error.
- Two "confidence" notions now coexist (a provider's, and the empirical
  calibrated accuracy); docs must keep the distinction clear.

## Implementation Notes

- Package: `aion-action-engine/packages/decision-engine` (`@aion/decision-engine`),
  dependency-light plain TS, `node:test`. Modules: `schemas/`, `providers/`,
  `policy/`, `telemetry/`, `evals/`, `engine.ts`.
- No live network in tests; `TypeSafeJevProvider` refuses calls without an
  `apiKey` and an allowed egress. Its response mapping is pure and tested.
- Records are designed to land in the AION event ledger; durable persistence and
  a dashboard are follow-up work.

## Follow-up Decisions

- **Revenue Copilot shadow integration** (record-only, no GHL writes) as the
  first experiment; success metrics: decision accuracy, calibration error,
  false-automation rate, human-disagreement rate, p50/p95 latency, cost/decision,
  LLM calls avoided.
- **Durable decision ledger** (schema in `aion-data`) and a calibration
  dashboard in Watchtower.
- **Where routing meets the Execution Gateway** — the exact hand-off between a
  Decision Plane route and the policy engine's `ALLOW / ASK / DENY`.
- Whether to promote a stable subset of the `DecisionProvider` contract into
  `aion-core` once a second plane consumer appears.
