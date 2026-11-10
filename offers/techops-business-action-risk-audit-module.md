# Business Action Risk Audit Module

- **Status:** Active commercial audit module (reusable)
- **Date:** 2026-11-09
- **Brief:** [../.aion/research/techops-monday-brief-2026-11-09.md](../.aion/research/techops-monday-brief-2026-11-09.md)
- **Vocabulary:** [../governance/business-action-risk-classification.md](../governance/business-action-risk-classification.md)
- **Front door:** [techops-governance-security-infrastructure-audit-v2.md](techops-governance-security-infrastructure-audit-v2.md)
- **SEL:** [../architecture/secure-execution-layer-v1.md](../architecture/secure-execution-layer-v1.md)

Reusable TechOps audit add-on that turns “what could an agent do?” into a
scored authority map and an Observe / Assist / Execute recommendation.

## SKU one-liner

> Classify every consequential automated action by financial impact, data
> sensitivity, reversibility, customer consequence, and authorization need —
> then price and engineer security to match authority, not model hype.

## Where it sits in the ladder

| Layer | Role of this module |
|---|---|
| **Audit / Foundation discovery** | Inventory workflows and classify actions |
| **Secure AI Execution** | Bind high-authority actions to gateway, approvals, evidence contract |
| **Continuous Assurance** | Monitor drift in permissions and behavior for high-authority actions |
| **Vertical packaging** | Accounting / dealer / rental / contractor language overlays |

## Delivery checklist

### Discovery

- [ ] List automations, agents, Zapier/n8n/MCP tools, and human-delegated bots
- [ ] For each consequential action, complete the five dimension scores
- [ ] Mark current mode (shadow Observe / Assist / Execute / unknown)
- [ ] Identify money, PII, irreversible, and customer-binding actions

### Classification workshop (≤ 90 minutes with ops + finance owner)

- [ ] Agree High-dimension actions that must be R3 / human-gated
- [ ] Agree Assist-only prepare paths (drafts without submit)
- [ ] Agree Observe-only analytics paths
- [ ] Record owners and dual-control needs

### Design output

- [ ] Action register (spreadsheet or controlled doc)
- [ ] Mode recommendations per action
- [ ] Evidence requirements mapped to
      [Agent Security Evidence Contract](../architecture/agent-security-evidence-contract.md)
- [ ] Pricing/implementation split: ordinary read-only vs high-assurance authority

## Scoring (audit module)

Score 0–3 per control. Add after AI/Agents domain in Audit v2.

| # | Control | 0 | 3 |
|---|---|---|---|
| BAR-01 | Consequential actions inventoried | Unknown bots | Named register with owner |
| BAR-02 | Dimension scores completed | Guesswork | Five dimensions per action |
| BAR-03 | Mode assigned (Observe/Assist/Execute) | Ambient Execute | Mode matches authority |
| BAR-04 | High-authority gates evidenced | Honor system | Approval + evidence pack |
| BAR-05 | Continuous monitoring scoped to High actions | None | CA checks mapped |

**Module subtotal:** __ / 15

### Hard stops

- BAR-01 = 0 → do not sell unattended Execute.
- Any High financial-impact action with BAR-04 = 0 → Foundation + SEL before automation go-live.

## Risk-based commercial model (to validate)

| Tier | Examples | Implementation posture |
|---|---|---|
| Ordinary | Read-only summarize, search, draft internal notes | Lower setup; standard Foundation |
| Governed Assist | Reconciliation prep, financing packet draft | Scoped tools + human review |
| High-assurance Execute | Payments, refunds, contract commits, record mutation | Extra engineering, dual control, evidence, Continuous Assurance |

Validate delivery cost and willingness to pay **before** publishing price cards.

## Vertical hooks

| Market | Lead language |
|---|---|
| Accounting | Secure Accounting AI Foundation — authority over books/payments |
| Auto dealers | Governed Dealership Operations — financing / voids / CRM writes |
| Rentals | Secure Rental Operations — deposits, refunds, pricing, contracts |
| Electrical | Pair with project reliability; money moves stay gated |

## Related

- [techops-secure-accounting-ai-foundation.md](techops-secure-accounting-ai-foundation.md)
- [techops-dealer-rental-security-overlay.md](techops-dealer-rental-security-overlay.md)
- [../governance/financial-action-policy.md](../governance/financial-action-policy.md)
