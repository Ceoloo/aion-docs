# ADR-006: Agent Trust Score (Execution Contract)

- **Status:** Accepted
- **Date:** 2026-09-14
- **Decision Owners:** Security / Permissions Engineer; Runtime Engineer

## Context

Task completion alone is a weak trust signal. Agents can “succeed” while using
the wrong tools, violating permissions, bypassing human gates, or burning
unbounded cost. Client-facing TechOps and Agent OS surfaces need a durable,
evidence-backed **Agent Trust Score** on each governed execution — not a
marketing claim of “automation successful.”

Core already persists the inputs: `ExecutionObject`, `EvaluationResult`,
`PolicyDecision` / policy events, `ApprovalRequest`/`ApprovalDecision`,
`ExecutionCost`, and `AutonomyEvidence`. There is no first-class Trust Score
contract yet. See TechOps Monday Brief (2026-09-14) and Linear AIO-19.

## Decision

1. **We will add an `AgentTrustScore` contract** (owned by `aion-core`) attached
   to an execution (and optionally summarized on durable outcomes). Runtime
   computes and exposes it; Data stores it.
2. **v1 measures exactly five dimensions:**
   - `taskCompletion`
   - `toolCorrectness`
   - `permissionCompliance`
   - `humanGateCompliance`
   - `executionCost`
3. **Each dimension is pass/fail + numeric evidence** derived only from existing
   telemetry — no new model-judgment harness for v1.
4. **Hard fails:** `permissionCompliance` or `humanGateCompliance` failing
   forces overall trust status `untrusted` regardless of completion/quality.
5. **Client card** is a projection of the score (checks + cost + confidence),
   not a separate truth store.
6. **Scoring rules live in**
   [governance/agent-trust-score.md](../governance/agent-trust-score.md).

## Alternatives Considered

- **Reuse only `CapabilityScorecard.rankingScore`** — Rejected as the client
  contract; routing weights optimize provider choice, not per-execution trust
  semantics (and soft-penalize human intervention rather than hard-fail gate
  bypass).
- **Full AgentAudit-style multi-axis research eval** — Deferred; too heavy for
  v1 and invents streams we do not yet own.
- **LLM-as-judge for tool correctness** — Deferred; v1 uses allow-list /
  policy-event evidence only.

## Consequences

### Positive

- Differentiates AION from “Zapier + LLM” with proveable execution trust.
- Couples cleanly to identity plane (ADR-005), human gates, and earned autonomy.
- Gives sales/ops a concrete card without waiting for a research eval farm.

### Negative

- Tool correctness v1 is coarse (allow-list / denial signals), not semantic
  tool-choice quality.
- Cost confidence depends on cost fields being populated on executions.

## Implementation Notes

- **Core:** add `AgentTrustScore` (+ dimension enums) under `src/contracts/`;
  pure function `computeAgentTrustScore(evidence)` with deterministic tests.
- **Runtime:** compute on execution terminalization / evaluation write; expose
  on execution/outcome read APIs.
- **Data:** persist JSON/columns mapped from Core camelCase → snake_case.
- **Docs:** governance spec is normative for v1 formulas.
- Do **not** invent parallel L-scales; autonomy remains L0–L4 (ADR-007 maps
  product tiers).

## Follow-up Decisions

- Semantic tool-correctness eval (when Intelligence Layer owns it).
- Aggregate Trust Score rollups for agents/services (feeds AutonomyEvidence).
- Optional dollar display currency normalization per tenant.
