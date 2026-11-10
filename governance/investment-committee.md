# AION Investment Committee (IC) Process

- **Status:** Active process (v0.1) — Holding ratification required for membership changes
- **Charter:** [../strategy/aion-universe-ecosystem-charter.md](../strategy/aion-universe-ecosystem-charter.md)
- **Registry:** [portfolio-registry.md](portfolio-registry.md)
- **Linear:** [AIO-88](https://linear.app/aion-empire/issue/AIO-88/aion-universe-ratify-ecosystem-charter-and-establish-product-admission)
- **Captured:** 2026-11-10 (America/New_York)

**Naming:** “Investment Committee” is the **AION Holding review process** for
portfolio admission and funding (standing Monday Holding deep-dive cadence).
It is **not** a newly authorized legal entity or parallel control plane.

The IC decides **whether** to allocate budget and capacity to a portfolio
entry. It does **not** authorize production deploys, customer-facing agent
writes, identity cutovers, or Forge worker activation. Those remain under
Runtime / Security / maintainer gates.

## Membership (initial)

| Seat | Role | Authority |
|---|---|---|
| Chair | AION Holding / CEO LOO | Final go / no-go / defer |
| Architecture | AION Systems Architecture | Platform boundary, reuse, control-plane risk |
| Runtime / Security | Runtime + Security owners | Live-gate and trust-boundary veto |
| Commercial / Delivery | AION Systems commercial owner | Capacity, margin, client delivery impact |
| Optional domain sponsor | Venture/product owner | Domain evidence only; no unilateral funding |

Until Holding names additional humans, the Chair may record a solo Holding
decision, but must still attach Architecture and Runtime/Security review notes
for any GROW spend that touches production or live customer systems.

## Cadence

- **Standing review:** monthly, or sooner when a funding request exceeds the
  standing experiment ceiling
- **Emergency pause:** Chair may PAUSE any INCUBATE entry that threatens P0
  production/client outcomes; record within 24 hours

## Standing experiment ceiling

Without a full IC packet:

- Documentation / research / fake-harness work: allowed under existing owners
- New recurring spend, new customer promises, new satellite repos, or any work
  that consumes protected production capacity: **requires IC decision**

Exact dollar ceilings are set by Holding and recorded in the decision log; do
not invent a budget in this process document.

## Admission checklist (required packet)

Before the IC meeting, the proposer files:

1. Named accountable owner and canonical repository
2. Actual customer/user problem, buyer, and demand evidence
3. Build vs integrate vs partner vs defer analysis
4. Data owner, tenant isolation, and permission model
5. Runtime adapter / service contract (if governed execution applies)
6. Delivery cost, margin hypothesis, pricing, distribution
7. Measurable first-value outcome and acceptance evidence plan
8. Security, reliability, support, and IP/licensing obligations
9. Kill condition, budget ceiling, and requested portfolio status
10. Impact on P0 (GE-001, CRM-001, Pilot-to-Revenue, client delivery)

## Decision outcomes

| Outcome | Meaning | Registry action |
|---|---|---|
| **FUND → GROW** | Operating priority with capacity | Set/confirm GROW |
| **FUND → VALIDATE** | Paid or instrumented proof only | Set VALIDATE; budget ceiling |
| **FUND → INCUBATE** | Bounded experiment | Set INCUBATE; hard stop date |
| **DEFER** | Not now; keep idea | No status promotion |
| **KILL / PAUSE** | Stop spend | Set PAUSE; archive packet |

## Decision record schema

Store under `.aion/decisions/` or link from Linear/Notion with this minimum:

```text
decision_id:
date:
proposer:
reviewers: [architecture, runtime_security, commercial]
request_summary:
portfolio_entry_id:   # PR-* from portfolio-registry.md
requested_status:
budget_ceiling:
capacity_impact_on_p0:
architecture_review: approve|revise|deny
runtime_security_review: approve|revise|deny|n_a
commercial_review: approve|revise|deny
chair_decision: FUND_GROW|FUND_VALIDATE|FUND_INCUBATE|DEFER|KILL_PAUSE
conditions:
kill_condition:
evidence_links: []
```

## Hard vetoes (non-waivable by popularity)

- Funding that delays CRM-001 Gate D or live GE-001 without explicit Chair
  acceptance of that delay
- Any claim that CI green / local tests equal production certification
- Enabling Forge workers or Hermes production scopes without AIO-121 / AIO-111
- Customer-facing autonomous writes without GE-001 PASS (or scoped equivalent)

## Related

- [change-management.md](change-management.md)
- [financial-action-policy.md](financial-action-policy.md)
- [../architecture/product-module-contract.md](../architecture/product-module-contract.md)
