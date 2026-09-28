# RES-002 — Friday Production Research Brief (2026-09-25)

**Status:** Research complete (recommendation). No prototype this slice.  
**Owner:** AION — RESEARCH  
**Input:** CEO / research brief — AWS CloudWatch Omni, Okta Blueprint Alliance, GitHub agentic-workflow study, anticipatory human oversight, Snowflake agent observability  
**Principle:** Research first. Prototype second. **Do not start a large subsystem this week.**

---

## Verdict

Industry vendors are independently converging on AION’s **control-plane** thesis (identity → task-scoped authority → gateway enforcement → telemetry → evals → economics → containment). That is strong external validation — **not** a mandate to rebuild the stack.

**Highest-leverage move:** upgrade the live Operator Loop acceptance target from “agent continues after approval” to **GOVERNED EXECUTION — VERIFIED**, by composing contracts that already exist (plus a thin certification field checklist). Do **not** start OTEL pipelines, Skills registries, Okta federation, or CloudWatch Omni adapters this week.

---

## Industry → AION mapping

| # | Industry signal | AION already has | Gap / trap | RESEARCH call |
|---|---|---|---|---|
| **1** | CloudWatch Omni: evals as operational telemetry on agent traces | M007 `EvaluationResult` ↔ `executionId`; scorecards; business-ID telemetry spine; execution lineage (`parent`/`root`) | No OTEL `trace_id`/`span_id`; evals attach to **executions**, not spans; OTLP is infra design intent only | **Certify execution-attached evals on the acceptance mission.** Defer OTEL export/adapters until an ADR + second consumer. **P0 = acceptance evidence, not Omni.** |
| **2** | Okta Blueprint: identity, task-scoped access, continuous observe, containment | ADR-005 Principal→Actor; `Capability`; `DelegatedAuthority` (+ `expiresAt`); M008 AutonomyGrant; FeatureGate kill-switch; gateway authorize | No mission-scoped capability grant type; kill-switch is domain feature-gate, not full `contain()` preserving state; authority not always first-class on HTTP execution records | **Use DelegatedAuthority + approval bind as the envelope in the acceptance run.** Spec `contain()` as design note only (P1). Do not integrate Okta yet. |
| **3** | GitHub agentic workflows: long executable specs; weak injection defenses | `Workflow` + MissionOrchestrator; Service Catalog `name@version`; agent governance cards | No SkillManifest; no skill lifecycle telemetry; injection policy not first-class on workflows | **Treat Workflow + ServiceDefinition as the executable spec for now.** Skills registry = premature platformization (one consumer). Add injection/budget fields to **acceptance workflow metadata**, not a new subsystem. |
| **4** | Anticipatory oversight: autonomy envelopes before execution | Risk R0–R3; human gates; M008 L0–L4 AutonomyGrant; PolicyEngine ALLOW/DENY/REQUIRE_APPROVAL | No first-class `mission_authority` object on Mission create; Operator Loop still centers **per-step approval** | **P0 design: Mission Authority Envelope as composition of existing types** (principal, agent, allowed capabilities/resources, risk max, spend/time limits, escalation, expires). Make approval the **exception path**. Implement as thin Core contract + Console capture **after** Operator Loop live PASS, not before. |
| **5** | Snowflake: quality + spend on agent paths; HTTP 200 ≠ business success | Economics rollups; EvaluationResult.qualityScore; outcomes; PRE-OL docs already warn runtime≠revenue success | No unified four-dim `mission_outcome`; `humanIntervention` is count not seconds; USD cost ledger deferred | **P0 schema proposal compose-only:** rollup Execution + Eval + Policy/Approvals + Economics into `MissionOutcome` view for acceptance. UI = P1. |

---

## What already covers the proposed certification fields

Proposed acceptance-record fields vs platform reality:

| Proposed field | Status |
|---|---|
| `principal_id` | **Exists** (ADR-005 Principal → Actor) |
| `delegation_chain` / authority | **Exists** as `DelegatedAuthority` (attenuating, `expiresAt`); not always persisted on every Execution HTTP view |
| `skill_id` / `skill_version` | **Exist as** `serviceKey` / Service Catalog `@version` + `Workflow.version` — do **not** invent Skills yet |
| `risk_class` | **Exists** (R0–R3 on capability/command) |
| `approval_required` / `approval_id` | **Exists** |
| `idempotency_key` | **Exists** on external side-effect contract |
| `trace_id` | **Partial:** `correlationId` / `runId` / execution lineage — **not** OTEL span IDs |
| `eval_result` | **Exists** (M007, 1:1 with `executionId`) |
| `model_cost` / `tool_cost` | **Partial** on `ExecutionCostBreakdown` |
| `outcome_value` | **Exists** (`OutcomeRecord` + Console terminalOutcome) |
| `authority_envelope_id` | **Thin gap** — mint/persist envelope id on mission start |
| `policy_version` | **Thin gap** — stamp policy/catalog versions on execution |
| `human_intervention_seconds` | **Gap** — today: approval counts only |
| `contain()` primitive | **Partial** — FeatureGate kill-switch + autonomy demote; not full reversible containment |

**Implication:** ~70% of the “add these fields” list is already in Core/Data/Runtime. The acceptance upgrade is mostly **evidence discipline + a few stamps**, not a greenfield schema rewrite.

---

## Recommended move this week (no large subsystem)

### Upgrade Operator Loop acceptance → GOVERNED EXECUTION — VERIFIED

Keep the existing live Lead-to-Appointment procedure in
`aion-runtime/docs/operator-loop-v1.md` as the **mission vehicle**. Raise the
**pass bar** to:

```text
1. Mission starts with explicit authority envelope (principal + agent + allowed caps/resources + risk max + spend/time limits + expires)
2. Registered agent receives only task-scoped capabilities for that mission
3. Agent proposes real GHL operation
4. Gateway evaluates execution context → AUTO | ASK | DENY
5. On ASK: operator decides; on DENY: no side effect
6. Approved execution occurs exactly once (idempotency key)
7. Trace + execution receipt recorded (lineage + side-effect IDs)
8. Mission survives restart/resume under same rootExecutionId
9. Eval scores at least one governed execution (quality + policy flags)
10. Economics records cost units + humanInterventions + outcome value
11. Production acceptance PASS only with the durable evidence pack
```

That one mission simultaneously exercises: orchestration, permissions, gateway, human gates, restart durability, observability spine, evals, economics — matching the brief’s intent without new vendors.

### Explicit NO-GO this week

| Proposal | Why NO-GO now |
|---|---|
| CloudWatch Omni / vendor APM adapter | Violates vendor-neutral spine; OTLP exporter needs ADR; zero second consumer |
| Okta Blueprint Alliance integration | Identity plane already ADR-005; federation is premature infra |
| New Skills registry / SkillManifest platform | Platformization rule fails (one product); Workflow + Service Catalog suffice |
| OTEL span hierarchy rewrite | Would destabilize M007 execution-attached evals mid-OL-001 |
| Full `contain()` control plane | Design only until Operator Loop live PASS exists |
| Adaptive routing / bandit | Still M007 non-goal |

### Ordered thin follow-ups (after GOVERNED EXECUTION PASS)

1. **P0 design note:** Mission Authority Envelope = composition of `DelegatedAuthority` + Mission limits + risk ceiling (Architect ADR if Core types expand).
2. **P0 schema compose:** `MissionOutcome` rollup view (execution / quality / governance / economics) — no new product UI required for v1.
3. **P1:** `human_intervention_seconds` (or approval wait-ms) on economics.
4. **P1:** `contain(agent_id)` design — revoke grants, reject new executions, terminate tool sessions, preserve state, emit event.
5. **P1/P2:** OTLP export seam (infra) consuming existing TelemetryRecord — CloudWatch/Grafana/Langfuse as backends, not authorities.
6. **P2:** Skills lifecycle only when a second product reuses the same executable-spec shape.

---

## Control-plane moat (agree)

```text
INTELLIGENCE PLANE          CONTROL PLANE (AION durable)
models / reasoning          identity · delegation · permissions
planning / memory           execution gateway · human authority
skills / orchestration      telemetry · evals · economics · containment
```

AION’s moat should remain:

> Govern autonomous work from delegated intent through execution, evidence, and economic outcome — regardless of which model or agent runtime performs the work.

This week’s AWS / Okta / GitHub-workflow / oversight / Snowflake signals all reinforce that split. They do **not** justify pausing OL-001 to build vendor-shaped shadows of the control plane we already certify via M001–M009.

---

## Milestone progression (adopt)

```text
RECOVERABLE — VERIFIED              ✓ (platform proofs / restart lineage)
OPERATOR LOOP — VERIFIED            → next live evidence pack
GOVERNED EXECUTION — VERIFIED       ← raise Operator Loop bar to this
ECONOMIC EXECUTION — VERIFIED
MULTI-AGENT EXECUTION — VERIFIED
MULTI-TENANT EXECUTION — VERIFIED
```

Do **not** measure progress by agent count or integration count.

---

## Next recommended actions

| Owner | Action |
|---|---|
| **Architect** | Accept/reject this brief; if accept, ADR scope for Mission Authority Envelope (compose vs new type) |
| **Runtime + Product** | Amend Operator Loop v1 evidence table to GOVERNED EXECUTION checklist; run one live OL-001 mission against it |
| **RESEARCH** | No prototype until Architect GO on envelope ADR; optional design-only note for `contain()` |
| **Integrations** | Stay on AIO-17 / GHL fidelity — orthogonal and already on the critical path |

---

## Kill criteria

- Any proposal that imports CloudWatch/Okta/Snowflake SDKs into `aion-core` or `aion-runtime`
- Replacing execution-attached evals with vendor-only span evals before OTLP ADR
- Building a Skills registry before a second product consumer
- Pausing OL-001 acceptance to chase industry dashboards
