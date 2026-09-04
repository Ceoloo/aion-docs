# ADR-003: The Execution Gateway — Idempotency, Replay Safety, and Execution Evidence

- **Status:** Accepted
- **Date:** 2026-09-04
- **Decision Owners:** Ceoloo (Founder / CEO)

## Context

AION already separates *deciding* from *doing*: the control-plane
[orchestrator](../architecture/control-plane.md) evaluates policy and routes work
to an [execution environment](../architecture/execution-layer.md) through one
common contract; a run pauses at a [human gate](../governance/human-gates.md)
(`awaiting_approval`) and resumes the **same** run on approval. AION Core's
current lifecycle is `Command → PolicyDecision → (deny | gate | execute) →
ExecutionResult → OutcomeReference`, with a full telemetry spine.

The [2026 Runtime-Control Brief](../research/2026-runtime-control-brief.md)
identifies a gap this lifecycle does not yet close. When runs become
**resumable and retried** — the direction every serious agent runtime is moving
(OpenAI Agents SDK 0.19–0.22 durable run state and safer retries) — a resumed or
retried worker can execute the **same external side effect twice**: send a
proposal twice, charge a card twice, deploy twice. AION has:

- **No idempotency key.** Nothing ties a state-changing intent to an
  at-most-once execution guarantee across retries and resumes.
- **No arguments fingerprint.** Nothing lets the gateway detect that a
  "retry" is actually a *different* action wearing the same id.
- **No execution receipt / evidence record.** `ExecutionResult` is returned to
  the caller and an `OutcomeReference` is minted, but there is no durable,
  queryable proof that *this exact side effect* ran, when, at what cost, and
  under which approval — the record a dedup check and an audit both need.

The brief also confirms (IBM CUGA, OpenAI PTC) that enforcement must remain
**structural and outside the model loop**. AION already satisfies this; the
missing pieces are the *at-most-once* guarantee and the *evidence* trail. We name
the enforcement boundary that carries them the **Execution Gateway**: the point
every state-changing tool call passes through, where policy clearance,
idempotency, sandboxing, and evidence capture are applied *before and around* the
side effect. It is not a new service — it is the hardened seam between the
control plane's decision and the execution environment's action.

There is no active mission that requires this to run in production today. This
ADR therefore ratifies the **contract and boundary**; concrete implementation is
scoped to design specs (aion-core, aion-data, aion-runtime) and gated on the
first mission whose actions are financial, destructive, or externally
observable — i.e. the first actions that make double-execution unacceptable.

## Decision

**We will formalize the Execution Gateway as the control-plane boundary through
which every state-changing execution passes, and give it three new
first-class contract concepts: an idempotency key, an arguments hash, and a
durable execution receipt (evidence).**

1. **Every command that can cause an external side effect carries an
   `idempotencyKey`.** It is caller-supplied when the caller can define a
   natural key (e.g. `invoice:INV-1042:send`), otherwise derived deterministically
   by the control plane from `(capability, arguments_hash, run scope)`. The
   gateway guarantees **at-most-once execution per idempotency key**: a second
   execution attempt for a key that already has a terminal receipt returns the
   original receipt instead of re-running the side effect.

2. **Every execution request carries an `argumentsHash`** — a stable, canonical
   hash of the capability arguments that actually reach the tool. It lets the
   gateway (a) detect a *key reuse with different arguments* as a conflict rather
   than a safe replay, and (b) prove, later, exactly what was executed without
   storing the (possibly sensitive) arguments themselves.

3. **Every completed execution produces a durable `ExecutionReceipt`** — the
   evidence record. It binds `run_id · idempotency_key · capability · tool_id ·
   arguments_hash · risk_level · approval_state · executor · model · status ·
   cost · started_at · completed_at · result_reference`. Receipts are
   **append-only and owned by aion-data**; the gateway consults them for the
   dedup check and emits them as the audit and economics source of truth.

4. **Replay safety spans the whole run, not just the tool call.** Resuming a run
   from `awaiting_approval` or retrying a failed step re-enters the gateway,
   which re-checks the idempotency ledger *before* dispatch. A resumed run never
   re-executes a step whose receipt is already terminal.

5. **The gateway remains vendor- and runtime-agnostic.** Idempotency, hashing,
   and receipts are defined in `aion-core` contracts and enforced by the
   orchestrator; no adapter (Claude Code, Cursor, Grok, a deterministic service,
   a human) is trusted to self-enforce them. This preserves
   [execution-layer](../architecture/execution-layer.md) coupling rule #1.

The receipt is distinct from an [event](../engineering/event-standards.md) and
from an [outcome](../architecture/learning-loop.md): an **event** is the
past-tense business fact (`proposal.sent`); a **receipt** is the operational
evidence that a governed side effect executed at-most-once; an **outcome** is the
real-world consequence (`customer paid`). Receipts *link* the three by shared IDs.

## Alternatives Considered

- **Rely on adapters / external SDKs for idempotency.** Rejected: it re-couples
  AION to a specific runtime's semantics (violating execution-layer rule #1),
  and gives no uniform, auditable guarantee across interchangeable runtimes and
  human execution. The guarantee must live at the AION boundary.
- **Put idempotency only in the tool implementations.** Rejected: most external
  tools (email, CRM, payments) offer inconsistent or no idempotency; the control
  plane cannot assume it and must not double-execute while waiting to find out.
- **Treat the existing telemetry record as the receipt.** Rejected: telemetry is
  append-only *observation* of steps and is intentionally lossy/sampled-capable;
  a receipt is an authoritative, per-side-effect ledger entry that a dedup check
  reads on the hot path. Overloading telemetry would make sampling unsafe.
- **A distributed lock / exactly-once semantics.** Rejected as over-reach:
  exactly-once across arbitrary external systems is not achievable in general.
  We commit to the honest, achievable guarantee — **at-most-once at the gateway,
  with idempotent retry returning the prior receipt** — and record it plainly.

## Consequences

### Positive

- A resumed or retried worker cannot silently repeat a financial, destructive,
  or external action — the single most dangerous failure mode of resumable
  agents.
- Every governed side effect gains a durable, queryable evidence record: the
  basis for audit, dispute resolution, and the [economics
  layer](../architecture/agent-economics.md) (ADR-004 reads receipts as its cost
  source of truth).
- The enforcement boundary is named and singular, making it obvious in review
  where at-most-once and evidence are guaranteed — and where they are missing.

### Negative

- New required contract fields (`idempotencyKey`, `argumentsHash`) and a new
  durable table (`execution_receipts`) add surface area and a hot-path read
  before dispatch. Accepted: the cost of a ledger lookup is small next to the
  cost of a duplicate payment.
- Deriving a good idempotency key is a modeling burden pushed partly onto
  callers/missions. Mitigated by a deterministic fallback and by requiring an
  explicit key only for R2+ side-effecting capabilities.
- Canonical argument hashing must be specified carefully (field ordering,
  normalization) or two equivalent calls hash differently. The core design spec
  owns that specification.

## Implementation Notes

- **aion-core** owns the contracts and enforcement: add `idempotencyKey` and
  `argumentsHash` to the command/execution path, define the `ExecutionReceipt`
  contract and an `IdempotencyStore`/`ReceiptSink` **port**, and enforce the
  dedup check in the orchestrator *before* adapter dispatch. Core stays
  database-agnostic — it defines ports, not storage. See
  [`aion-core/docs/design/execution-gateway.md`].
- **aion-data** owns durability: the `execution_receipts` table and the
  idempotency ledger (or the receipt table serving both roles), append-only,
  keyed for the dedup lookup. Governed by
  [data-contracts](../engineering/data-contracts.md). See
  [`aion-data/docs/design/economics-and-idempotency.md`].
- **aion-runtime** wires the concrete store/sink into the composition root (the
  same way it wires the run repository and event sink today), per
  [ADR-002](ADR-002-runtime-host-ownership.md). See
  [`aion-runtime/docs/design/execution-gateway-composition.md`].
- **Dependency direction** is respected: core defines ports; data implements
  them; runtime composes; infra deploys the image. No new cross-repo edges.
- **Risk coupling:** the gateway's at-most-once guarantee is *required* for R2+
  side-effecting capabilities and *recommended* for R1. R0 (read/draft) needs no
  receipt. See [risk-levels](../governance/risk-levels.md).

## Follow-up Decisions

- **ADR-004** (Agent Economics) consumes receipts as the cost/value source.
- A future ADR selects the **storage/index** specifics if the receipt hot-path
  read outgrows the primary Postgres table (deferred until measured).
- A future ADR specifies the **sandbox** that programmatic execution runs in;
  idempotency for multi-tool sandboxed plans is a superset of this ADR and is
  addressed there. See
  [`aion-core/docs/design/programmatic-execution-and-a2a.md`].
