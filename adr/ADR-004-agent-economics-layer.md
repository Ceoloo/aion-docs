# ADR-004: Agent Economics as a First-Class Layer

- **Status:** Accepted
- **Date:** 2026-09-04
- **Decision Owners:** Ceoloo (Founder / CEO)

## Context

AION's [observability spine](../architecture/observability.md) already treats
cost as first-class: every telemetry record carries `token/cost usage`,
`risk_level`, `approval_state`, and an `outcome_reference`, and the doctrine
keeps a *result* (what an execution produced) strictly distinct from an
*outcome* (the real-world consequence) — see
[principles #6](../engineering/principles.md).

The [2026 Runtime-Control Brief](../research/2026-runtime-control-brief.md)
(signal 3, Microsoft Foundry) shows the field moving observability from
**trace → token count** to **trace → quality → cost → business value**, with
explicit agent-ROI accounting: operating cost vs. task completion, time saved,
cost efficiency. AION has the *inputs* for this (cost on telemetry, outcomes with
monetary `value`/`currency` in aion-data) but no **layer that composes them into
economics**: cost per successful task, automation efficiency, revenue ROI, agent
utility. Without it, AION can say what an agent *did* and what it *cost*, but not
whether it was *worth it* — the question the CEO dashboard and Agent-OS
performance scoring exist to answer.

Two forces make this more than internal monitoring. First, ROI accounting is a
credible *product* surface ("AION tells you whether your autonomous work paid
for itself"), not merely ops telemetry. Second, doing it wrong — mixing
estimated value into the authoritative cost ledger, or letting a worker
self-report its own ROI — would corrupt exactly the signal the
[learning loop](../architecture/learning-loop.md) depends on.

There is no active mission requiring live economics today. This ADR ratifies the
**layer, its inputs, and its ownership**; implementation is design-spec'd and
gated on Phase 5 (outcome instrumentation) per the
[build order](../roadmap/build-order.md).

## Decision

**We will define Agent Economics as a first-class, vendor-neutral derived layer,
owned by `aion-data`, computed from authoritative cost (execution receipts and
telemetry) and realized value (outcomes) — never self-reported by workers.**

1. **The economics record is per-agent-run** and composes four input families:
   - **cost** — `compute_cost`, `tool_cost`, `tokens`, from the
     [execution receipts](ADR-003-execution-gateway-and-evidence.md) and
     telemetry (authoritative; not estimated);
   - **completion & quality** — `task_result`, `eval_score`;
   - **realized value** — `human_minutes_saved`, `revenue_influenced`,
     `revenue_created`, from `outcomes` (measured, lineage-preserved);
   - **derived ROI** — computed, never stored as if measured.

2. **Derived metrics are defined once, canonically:**
   - **Cost per successful task** = `total_agent_cost / successful_tasks`
   - **Automation efficiency** = `human_time_saved / agent_cost`
   - **Revenue ROI** = `revenue_attributed / agent_cost`
   - **Agent utility** = `successful_outcomes × quality / total_cost`

   These are **analytics contracts** (a
   [data kind](../architecture/data-layer.md)) with explicit lineage back to the
   receipts, telemetry, and outcomes they derive from — not free-floating
   numbers.

3. **Cost is authoritative; value is measured; ROI is derived.** The three are
   kept in distinct records with distinct trust levels. Estimated or attributed
   value is flagged as such and never overwrites measured cost. A worker cannot
   write its own economics row — economics is computed *about* runs, by the data
   layer, from evidence.

4. **Economics is vendor-neutral.** `compute_cost` and `tool_cost` are recorded
   in AION's abstract cost units at capture time (Core already uses abstract
   units), with an owned, versioned unit→currency mapping applied in the
   economics layer — so switching model vendors or renegotiating tool pricing
   re-prices history through one contract, not a schema change.

5. **Economics may become product.** Products consume economics **through
   contracts** ([data-contracts](../engineering/data-contracts.md),
   [dependency rules](../repositories/dependency-rules.md) #5) — the CEO
   dashboard and any customer-facing ROI view read the canonical economics
   contract; they never fork it or compute their own competing definition.

## Alternatives Considered

- **Extend telemetry with ROI fields.** Rejected: telemetry is per-step
  operational observation and is sampling-tolerant; economics is a per-run
  derived aggregate that must be complete and authoritative. Different trust
  levels, different lifecycles.
- **Compute ROI in each product / the dashboard.** Rejected: it forks the
  definition (dependency rule #4/#5), so two dashboards disagree on "ROI". One
  canonical contract in aion-data, consumed by all.
- **Let workers report time-saved / revenue-influenced.** Rejected: self-reported
  value is the corruption the learning loop must never ingest
  ([event-standards](../engineering/event-standards.md): facts, not wishes).
  Value comes from measured outcomes.
- **Store a single blended `roi` number.** Rejected: it hides whether the gain
  is cost reduction (efficiency) or revenue (ROI). We keep the four metrics
  distinct.

## Consequences

### Positive

- AION can answer *"was this autonomous work worth its cost?"* per agent, per
  mission, and in aggregate — powering the CEO dashboard and Agent-OS scoring.
- Cost stays honest (from receipts/telemetry) and value stays honest (from
  measured outcomes); ROI is transparently derived, so a bad number is traceable
  to a bad input, not a fudge.
- Creates a defensible product surface: ROI accounting as part of the Agent OS,
  not an afterthought.

### Negative

- Requires the [execution receipts](ADR-003-execution-gateway-and-evidence.md)
  and Phase-5 outcome instrumentation to be real before the numbers mean
  anything. Until then, economics is partial and must be labeled as such.
- Attributing `revenue_influenced` to an agent run is genuinely hard and
  partly subjective; the layer must expose attribution method and confidence,
  not launder an estimate into a fact.
- The unit→currency and attribution mappings are new owned contracts that must
  be versioned and governed like any other schema.

## Implementation Notes

- **aion-data** owns the economics contracts, storage, and derived views
  (`agent_economics` per run; ROI/efficiency as analytics views with lineage).
  Value inputs come from `outcomes`; cost inputs from `execution_receipts` and
  `telemetry_records`. See
  [`aion-data/docs/design/economics-and-idempotency.md`] and
  [`architecture/agent-economics.md`](../architecture/agent-economics.md).
- **aion-core** ensures cost is *captured* honestly at the gateway (split
  `compute_cost` vs `tool_cost` where knowable; carry `eval_score` reference) but
  does **not** compute or store economics — core decides and coordinates; it does
  not own derived data.
- **aion-products** consumes the economics contract for the CEO dashboard through
  the read path only. See
  [`aion-products/docs/design/agent-economics-consumption.md`].
- **Phasing:** aligns with [build order](../roadmap/build-order.md) Phase 5
  (outcome instrumentation) and Phase 6 (learning). No live economics is built
  before a mission produces real outcomes to measure.

## Follow-up Decisions

- A future ADR fixes the **revenue-attribution method** (last-touch, influenced,
  model-based) when the first revenue mission needs it — deferred, not guessed.
- A future ADR sets the **unit→currency mapping governance** (who updates it, how
  history re-prices) at first real spend.
