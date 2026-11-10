# AION Client Onboarding & Delivery Playbook

- **Status:** Active delivery standard (v0.1) — closes Linear [AIO-8](https://linear.app/aion-empire/issue/AIO-8/standardize-aion-client-onboarding-and-delivery-workflow)
- **Date:** 2026-11-10 (America/New_York)
- **Commercial rules:** assessment/strategy, implementation, advisory, and usage costs stay separate
- **Technical gate:** live customer-facing automated writes require GE-001 PASS (or scoped equivalent)

This playbook turns a signed client into a repeatable path from kickoff to
measured outcome and expansion. It does **not** authorize unverified production
agent writes.

## 1. End-to-end workflow

| Step | Owner | Exit criteria |
|---|---|---|
| 1. Qualification / discovery | Commercial | Fit call notes; ICP match; feasibility flags |
| 2. Audit and baseline | Delivery + client | Dated baseline (leads, response time, bookings, cash proxies) |
| 3. Proposal + scope | Commercial | Signed SOW using [implementation-scope-template.md](implementation-scope-template.md) |
| 4. Payment / setup | Commercial / finance | Kickoff invoice collected or contractually secured |
| 5. Access + onboarding | Delivery | Access checklist complete; credentials vaulted; RACI named |
| 6. Build / configure | Delivery | Configured against SOW; no out-of-scope silent adds |
| 7. QA + launch | Delivery + client | Acceptance tests pass; go-live recorded |
| 8. Measure ROI / impact | Delivery | Baseline vs result pack; ledger links |
| 9. Client review | Commercial + delivery | Monthly/quarterly review notes |
| 10. Expansion / support | Commercial | Upsell only on evidenced need; CO for scope changes |

## 2. Kickoff access checklist

- [ ] Named client sponsor and day-to-day operator
- [ ] CRM (Twenty / approved system) tenant + least-privilege access
- [ ] Calendar / communications connectors as scoped
- [ ] Analytics / traffic baseline export (if applicable)
- [ ] Brand/assets / content sources (Creative engagements)
- [ ] Billing contact and invoice method
- [ ] Security constraints / MFA requirements documented
- [ ] Data retention / offboarding expectations acknowledged

Never paste secrets into tickets, chat, or PRs.

## 3. Delivery ownership (RACI summary)

| Activity | Client | AION Delivery | AION Commercial | Runtime/Security |
|---|---|---|---|---|
| Business goals & approvals | A | C | R | I |
| SOW / change orders | C | C | A/R | I |
| System configuration in scope | C | R/A | I | C (gates) |
| Live governed writes | C | R | I | A (policy) |
| Outcome measurement | C | R | A | I |
| Support triage | C | R | A | C |

A = accountable, R = responsible, C = consulted, I = informed.

## 4. Acceptance package (minimum)

1. Signed SOW revision hash / date
2. Configured workflow inventory (sources, CRM stages, calendars)
3. Test evidence (positive + denial/negative where governed)
4. Baseline metrics with capture date
5. Go-live timestamp and operator identity
6. Links into commercial ledger (`agreement_id`, first invoice)
7. Open risks and deferred items list

## 5. Technical non-negotiables

- Observe / Assist / Execute permissions respected
- No customer-facing autonomous send/publish without applicable gate
- CRM writes use approved provider path (Twenty after CRM-001 Gate D)
- Change requests use [change-order-template.md](change-order-template.md)
- Support boundaries follow [support-tiers.md](support-tiers.md)

## 6. ModernRelx / existing-client note

Existing workspaces (e.g. ModernRelx Growth HQ) remain under their signed
terms. Apply this playbook prospectively: confirm scope/assets/access, capture
dated baselines, verify purchase/analytics events where claimed, and accept one
scoped milestone before expansion.

## Related

- [implementation-scope-template.md](implementation-scope-template.md)
- [change-order-template.md](change-order-template.md)
- [support-tiers.md](support-tiers.md)
- [client-profitability-scorecard.md](client-profitability-scorecard.md)
- [aion-pilot-to-revenue-program.md](aion-pilot-to-revenue-program.md)
- Data `implementation_cases` migration `0011` (delivery state machine)
