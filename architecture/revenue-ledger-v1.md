# Revenue Ledger v1 — Commercial Truth Contract

- **Status:** Active architecture contract (v0.1)
- **Date:** 2026-11-10 (America/New_York)
- **Linear:** [AIO-15](https://linear.app/aion-empire/issue/AIO-15/implement-aion-governance-memory-learning-observability-and-economic), revex fixes AIO-116/117/118
- **Commercial companion:** [../offers/client-profitability-scorecard.md](../offers/client-profitability-scorecard.md)

Growth is economically unsafe when booked revenue, invoices, collected cash,
delivery cost, and client margin live in disconnected spreadsheets. This
contract separates **systems of record** so agents and operators cannot confuse
pipeline theater with cash.

## 1. Ledger planes (do not collapse)

| Plane | System of record | What it proves | What it is not |
|---|---|---|---|
| **CRM commercial state** | Twenty (after CRM-001 Gate D) | Contacts, opportunities, stages | Invoice/AR truth |
| **Governed side effects** | AION Data `external_side_effects` + Runtime | Approved external mutations + idempotency | USD P&L |
| **Runtime economics** | AION Data economics / cost units | Execution cost proxies, ROI rollups | Priced USD expense |
| **Revex / revenue ops** | Supabase revex tables (legacy island) | Outreach/revenue event ops for Revenue Factory | Canonical AR |
| **Commercial ledger** | Finance workbook → future `aion_finance` (this contract) | Invoices, collections, direct costs, client margin | CRM stage changes |

Until a priced finance store exists in `aion-data`, the **Commercial ledger**
may be an operator-controlled workbook that follows this schema. Mark every
automated field `VERIFIED` or `UNVERIFIED`.

## 2. Canonical commercial entities

### 2.1 `clients`

| Field | Rule |
|---|---|
| `client_id` | Stable AION id |
| `legal_name` | Contractual name |
| `crm_provider` / `crm_account_ref` | Twenty (or legacy) reference only |
| `owner` | Named commercial owner |
| `status` | prospect / active / paused / churned |

### 2.2 `agreements`

| Field | Rule |
|---|---|
| `agreement_id` | Stable id |
| `client_id` | FK |
| `sku` / offer name | From approved catalog / SOW |
| `setup_fee_usd` | One-time |
| `mrr_usd` | Recurring; 0 if none |
| `support_hours_included` | Cap |
| `change_order_policy_ref` | Link to CO process |
| `signed_at` / `effective_from` / `effective_to` | Dates |
| `source_of_truth` | Signed PDF / contract system URI |

### 2.3 `invoices`

| Field | Rule |
|---|---|
| `invoice_id` | External or internal id |
| `agreement_id` | FK |
| `period_start` / `period_end` | Service period |
| `amount_usd` | Billed |
| `status` | draft / sent / partially_paid / paid / void / written_off |
| `issued_at` / `due_at` | Dates |
| `payment_provider_ref` | Stripe/bank/etc. |
| `evidence_ref` | Invoice PDF / provider export |

### 2.4 `collections`

| Field | Rule |
|---|---|
| `collection_id` | Stable id |
| `invoice_id` | FK |
| `amount_usd` | Collected cash |
| `collected_at` | Bank/provider timestamp |
| `evidence_ref` | Bank/provider export |
| `reconciled_by` | Named human |

**Hard rule:** CRM `won` or opportunity amount is **never** collected cash.
GE-001 attributed cash must reference a `collections` row or be stamped
`UNVERIFIED`.

### 2.5 `direct_costs`

| Field | Rule |
|---|---|
| `cost_id` | Stable id |
| `client_id` / `agreement_id` | Attribution |
| `category` | labor / model_provider / communications / cloud / software / subcontractor / other |
| `amount_usd` | Direct cost |
| `period` | Month or engagement window |
| `source` | Timesheet / invoice / provider bill |
| `verification` | VERIFIED / UNVERIFIED |

Runtime `totalCostUnits` may inform investigation but **does not** populate
`amount_usd` unless a priced conversion source is attached.

### 2.6 `client_profitability_snapshot`

Monthly (or engagement) rollup:

```text
revenue_recognized_usd   = invoices in period that are paid or contractually recognized
collected_cash_usd       = sum(collections)
direct_cost_usd          = sum(direct_costs)
gross_profit_usd         = revenue_recognized_usd - direct_cost_usd
gross_margin_pct         = gross_profit_usd / revenue_recognized_usd  (if revenue > 0)
support_hours_used
change_orders_count
verification_notes
```

Target guardrails (commercial hypotheses, not guarantees): setup ≥50% gross
margin; recurring ≥60% after standardization. See offer catalog.

## 3. Invoice reconciliation procedure

Run at least monthly, and before any “profitable client” claim:

1. Export invoices from billing system for the period
2. Export collections / bank deposits for the period
3. Match `invoice_id` ↔ payment refs; mark unmatched as exceptions
4. Confirm CRM opportunity amounts are **not** used as cash substitutes
5. Attach provider/billing exports as `evidence_ref`
6. Record reconciler name + timestamp
7. File exceptions (missing payment, overpayment, credit, dispute)

### Reconciliation statuses

| Status | Meaning |
|---|---|
| `matched` | Invoice and collection agree within tolerance |
| `partial` | Partial payment; remaining AR open |
| `unmatched_invoice` | Billed, no collection evidence |
| `unmatched_collection` | Cash without invoice link |
| `disputed` | Client/commercial dispute open |

## 4. Cost tracking procedure

1. Attribute labor hours to `agreement_id` (delivery time tracking)
2. Import model/provider bills; allocate by client when possible, else shared
   platform pool with explicit note
3. Allocate cloud/software connectors used for that client’s workloads
4. Capture subcontractors against the engagement
5. Mark each line VERIFIED (source attached) or UNVERIFIED
6. Roll into `client_profitability_snapshot`

## 5. Relationship to existing platform ledgers

| Existing artifact | Keep doing | Do not |
|---|---|---|
| Side-effect ledger (`0008`) | Prove governed writes | Treat as AR |
| Revenue sessions (`0009`) | Copilot/session continuity | Treat as invoices |
| Economics repository | Execution cost/ROI proxies | Auto-claim USD margin |
| Revex (AIO-116/117/118) | Fix ops bugs; local proof then live apply | Expand as canonical finance |
| GE-001 economics fields | Require priced provider expense or UNVERIFIED | Use cost units as USD |

## 6. Implementation sequence (gated)

| Step | Work | Gate |
|---|---|---|
| L0 | Operate workbook/schema above for active clients | Commercial owner named |
| L1 | Close AIO-116/117/118 live apply + evidence | Maintainer |
| L2 | Optional `aion-data` finance migration ADR | Architecture + Holding |
| L3 | Billing connector (read-only first) | Security + financial-action policy |
| L4 | Client profitability dashboard in Intelligence | After L0 reconciliation exists |

## 7. Explicit non-authorizations

- This document does not change customer prices or publish a rate card
- This document does not authorize autonomous payment initiation
- Agents may **prepare** reconciliation drafts; humans **approve** matched books
  ([financial-action-policy.md](../governance/financial-action-policy.md))

## Related

- [../offers/client-onboarding-playbook.md](../offers/client-onboarding-playbook.md)
- [../offers/client-profitability-scorecard.md](../offers/client-profitability-scorecard.md)
- [../.aion/patches/aio-116-117-118-revex-ledger.md](../.aion/patches/aio-116-117-118-revex-ledger.md)
- [../roadmap/governed-execution-certify.md](../roadmap/governed-execution-certify.md)
