# Agent Trust Score

Normative v1 scoring rules for [ADR-006](../adr/ADR-006-agent-trust-score.md).
Trust Score answers: *did this governed execution earn trust, not merely finish?*

## Client card (projection)

```
Outcome achieved          ✓ / ✗
Correct tools used        ✓ / ✗
Permissions respected     ✓ / ✗
No prohibited action      ✓ / ✗   (same evidence as permissions; explicit copy)
Human gate respected      ✓ / ✗
Cost                      $X.XX (or abstract units when $ unknown)
Execution confidence      N%
```

`No prohibited action` is a **display alias** of `permissionCompliance` (includes
forbidden capability / data-scope / tool denials). It is not a sixth dimension.

## Schema sketch (Core)

```ts
type TrustDimensionId =
  | 'taskCompletion'
  | 'toolCorrectness'
  | 'permissionCompliance'
  | 'humanGateCompliance'
  | 'executionCost';

type TrustDimension = {
  id: TrustDimensionId;
  passed: boolean;
  /** 0..1 contribution or cost fitness; null when unknown */
  score: number | null;
  evidence: string[]; // short machine-stable codes, e.g. 'execution.status=succeeded'
};

type AgentTrustScore = {
  executionId: string;
  computedAt: string; // ISO datetime
  status: 'trusted' | 'degraded' | 'untrusted' | 'incomplete';
  /** 0..100 integer confidence for the card */
  confidence: number;
  dimensions: TrustDimension[];
  cost: {
    units: number | null;
    usd: number | null;
    currency: 'USD' | null;
  };
};
```

## Evidence sources (existing only)

| Dimension | Primary sources |
|---|---|
| taskCompletion | `ExecutionObject.status`, `EvaluationResult.success`, optional `OutcomeReference.status` |
| toolCorrectness | tools in `auditTrace` / telemetry ⊆ `AgentActor.allowedTools`; `EvaluationResult.policyEvents` tool DENYs; optional `qualityScore` floor |
| permissionCompliance | terminal `PolicyDecision`; any DENY on `permission` / `data-scope` / `tool` / `tenant-scope` / `identity`; `forbiddenCapabilities` hits |
| humanGateCompliance | if `REQUIRE_APPROVAL` or status `awaiting_approval`: must have `ApprovalDecision.approve=true` from human Actor before side effects; timeout/reject → fail; gate-required path that executed without approval → fail |
| executionCost | `ExecutionObject.cost` / `EvaluationResult.totalCost` vs `AgentActor.costBudget` or request `budgetRemaining` |

## Pass / fail rules (v1)

### 1. taskCompletion

- **Pass** if execution terminal status is `succeeded` **and** (if evaluation
  present) `EvaluationResult.success === true`.
- If an OutcomeReference is `failed`, **fail** even if execution succeeded
  (outcomes outrank generations — see [evals.md](../engineering/evals.md)).
- **Incomplete** if still non-terminal (`created`, `evaluating`,
  `awaiting_approval`, …).

### 2. toolCorrectness

- **Pass** if every recorded tool id is in the actor’s `allowedTools` (when that
  list is non-empty) **and** there is no tool-kind policy DENY in
  `policyEvents`.
- If `allowedTools` is empty/undefined, treat as “no allow-list declared”:
  **pass only if** no tool DENY events; mark evidence `toolAllowList=absent`
  (confidence penalty — see below).
- Optional soft signal: if `qualityScore` present and `< 0.5`, dimension
  **fails** (coarse stand-in until semantic tool eval exists).

### 3. permissionCompliance (hard)

- **Fail** if any authorizing decision is `DENY`, or any policy event with
  `decision: DENY` for permission/data-scope/tool/tenant/identity checks.
- **Fail** if execution status is `denied`.
- **Pass** only when authorize path was `ALLOW` or completed
  `REQUIRE_APPROVAL` → granted, with no DENY events.
- **Hard-fail rule:** failure here ⇒ overall `status = untrusted`.

### 4. humanGateCompliance (hard)

- If policy never required a gate for this execution: **pass** with evidence
  `gate=not_required`.
- If gate required:
  - **Pass** only if an ApprovalDecision exists with `approve: true`,
    `decidedBy` is a human Actor, and the execution did not perform gated side
    effects before grant (status progression respected).
  - **Fail** on reject, expiry without grant, missing decision, or
    non-human/spoofed approver (ADR-005).
- **Hard-fail rule:** failure here ⇒ overall `status = untrusted`.

### 5. executionCost

- Let `units` = recorded cost units (required for a numeric score).
- **Pass** if budget ceiling known and `units ≤ ceiling`.
- **Pass** with `score=null` evidence `budget=unbounded` if no ceiling
  configured (confidence penalty).
- **Fail** if `units > ceiling` or budget check DENY occurred.
- Card `$` amount: use dollar fields when present; otherwise show units and omit `$`.

## Overall status & confidence

```
if any hard dimension failed → status = untrusted
else if any dimension incomplete / missing critical evidence → status = incomplete
else if taskCompletion & toolCorrectness & executionCost all passed
     and no confidence penalties → status = trusted
else → status = degraded
```

**Confidence (0–100)** — deterministic v1:

| Base | Rule |
|---|---|
| 100 | start when all five dimensions have conclusive evidence |
| −25 | each hard-fail dimension |
| −15 | taskCompletion fail |
| −10 | toolCorrectness fail or `toolAllowList=absent` |
| −10 | executionCost fail or `budget=unbounded` |
| −10 | evaluation missing on a succeeded execution |
| −20 | outcome missing when workflow declares outcome-required |
| floor | 0 |

Trusted executions with full evidence typically land **≥ 90**. Untrusted
executions still show confidence to communicate *how* broken the run was, but
UI must label status `untrusted` prominently.

## Relationship to AutonomyEvidence / routing scores

- Trust Score = **per-execution** judgment.
- `AutonomyEvidence` / `CapabilityScorecard` = **aggregates** over many
  executions (promotion / routing).
- Aggregates **must** count `untrusted` executions as policy violations / failed
  samples — never as successes that earn L4.

## Invariants

- Hard dimensions cannot be waived by quality or cost savings.
- Workers cannot self-assert Trust Score; Runtime computes from durable records.
- No sixth v1 dimension without a new ADR.
