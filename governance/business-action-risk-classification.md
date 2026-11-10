# Business Action Risk Classification

- **Status:** Active TechOps audit vocabulary
- **Date:** 2026-11-09
- **Brief:** [../.aion/research/techops-monday-brief-2026-11-09.md](../.aion/research/techops-monday-brief-2026-11-09.md)
- **Platform risk levels:** [risk-levels.md](risk-levels.md) (R0–R3)
- **Action tiers:** [action-tiers.md](action-tiers.md) (Observe / Assist / Execute)
- **Financial loop:** [financial-action-policy.md](financial-action-policy.md)
- **Commercial module:** [../offers/techops-business-action-risk-audit-module.md](../offers/techops-business-action-risk-audit-module.md)

AI risk for SMB operations should be classified by the **authority granted to
the workflow**, not by how sophisticated the model looks.

Regulatory signal (OCC supervisory framework, Nov 2026): examiners are directed
toward materiality and consequences of AI use. The framework does **not** create
new enforceable bank rules for AION customers and does **not** directly regulate
accounting firms — it validates AION’s architecture bias toward action
consequences.

## 1. Classification dimensions

Score each automated (or automatable) business action on:

| Dimension | Low | Moderate | High |
|---|---|---|---|
| **Financial impact** | None / reporting only | Drafts affecting money later | Moves money, credit, pricing, refunds |
| **Data sensitivity** | Public / internal non-PII | Customer PII / tax / HR | Regulated secrets, full financial records |
| **Reversibility** | Easily undo | Undo with effort / partial | Irreversible or legally binding |
| **Customer consequences** | None / internal | Delayed customer effect | Immediate customer, legal, or safety effect |
| **Authorization requirements** | None beyond login | Dual control / maker-checker preferred | Named human approval mandatory |

## 2. Mode mapping

| Classification outcome | Default mode | Platform risk |
|---|---|---|
| Mostly Low dimensions | **Observe** | R0–R1 |
| Mixed / Moderate without money movement | **Assist** (prepare only) | R1–R2 |
| Any High financial impact, irreversible, or customer-binding | **Execute only with gate** | R3 (+ financial-action loop) |

Workers never self-lower classification. Central policy owns the mapping
([risk-levels.md](risk-levels.md) invariants).

## 3. Accounting workflow examples

| Workflow | Profile | Recommended control |
|---|---|---|
| Summarize financial documents | Lower operational authority | Read-only access + data controls (Observe) |
| Prepare reconciliation adjustments | Moderate authority | Scoped access + human review (Assist) |
| Execute payments or modify financial records | High authority | Explicit approval, limits, durable evidence (Execute + Verify) |

## 4. Cross-vertical examples

| Vertical | Observe | Assist | Execute (gated) |
|---|---|---|---|
| Dealership | Inventory / CRM read | Draft financing packet | Submit financing, voids |
| Rental | Availability / rates read | Draft contract / refund request | Capture deposit, refund, price override |
| Electrical contractor | Schedule / doc read | Draft PO / change-order packet | Commit PO, payment, contract money move |

## 5. Audit output

For each in-scope action, record:

```text
action_id | description | dimension scores | mode | risk_level
  | approval_requirement | evidence_requirement | owner
```

Feed results into Secure AI Execution design and Continuous Assurance monitoring
scope. High-authority workflows become the higher-assurance commercial tier.

## Related

- [../architecture/agent-security-evidence-contract.md](../architecture/agent-security-evidence-contract.md)
- [../architecture/secure-execution-layer-v1.md](../architecture/secure-execution-layer-v1.md)
- [human-gates.md](human-gates.md)
