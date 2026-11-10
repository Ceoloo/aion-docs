# AION Support Responsibilities & Tiers

- **Status:** Active commercial/operations standard (v0.1)
- **Date:** 2026-11-10
- **Maps to:** [../governance/action-tiers.md](../governance/action-tiers.md) Observe / Assist / Execute
- **Onboarding:** [client-onboarding-playbook.md](client-onboarding-playbook.md)

Support is a **bounded contractual service**, not unlimited IT. Hours, channels,
and response targets are agreement-specific; the tables below are the default
framework for new SOWs.

## 1. Responsibility split

| Domain | AION | Client |
|---|---|---|
| In-scope configured workflows | Monitor, triage, fix within hours | Provide timely access and decisions |
| Out-of-scope systems | Advise only | Own vendor/IT |
| Credentials / MFA | Recommend least privilege | Own identity hygiene |
| Content / offers / pricing truth | Configure approved rules | Own business truth |
| Incidents on client SaaS | Assist investigation | Own vendor tickets |
| Change requests | Quote via CO | Approve/deny |

## 2. Support tiers (defaults for new agreements)

| Tier | Typical inclusion | Channel | Business-hours target* | After-hours |
|---|---|---|---|---|
| **T0 — Audit / project only** | Defects in delivered project during acceptance window | Email / ticket | 2 business days | Best effort |
| **T1 — Advisory retainer** | Guidance + monthly review; no unlimited ops | Scheduled + ticket | 1 business day | None |
| **T2 — Managed Revenue / Ops** | Included monthly hours; exception triage; monthly report | Ticket + shared channel | 4 business hours for Sev-2+ | Sev-1 path if contracted |
| **T3 — Continuous Assurance** | Bounded connector reviews + monthly risk briefing | Ticket + scheduled | Per assurance SOW | Explicitly scoped only |

\*Business hours default: America/New_York weekdays unless SOW states otherwise.
These targets are **service aims**, not SLA credits, unless the signed contract
adds credits.

## 3. Severity

| Sev | Definition | Default response aim (T2) |
|---|---|---|
| Sev-1 | Production workflow down; customer-impacting outage in AION-operated scope | 1 business hour acknowledge |
| Sev-2 | Major degradation; workaround possible | 4 business hours |
| Sev-3 | Minor defect / question | 1 business day |
| Sev-4 | Enhancement / CO candidate | Backlog → change order |

## 4. Hour caps and overage

- Track support/optimization minutes against the monthly included cap
- At 80% consumption, notify commercial + client
- Beyond cap: pause discretionary work or bill overage / issue CO
- Recurring “urgent” work that is really scope expansion → [change-order-template.md](change-order-template.md)

## 5. Observe / Assist / Execute in support

| Mode | Support may | Support may not |
|---|---|---|
| Observe | Read logs, health, approved dashboards | Expand data access silently |
| Assist | Draft fixes, messages, configs for approval | Send customer communications without approval when gated |
| Execute | Apply approved in-scope changes | Autonomous production deploys or financial actions |

## 6. Explicit non-inclusions (unless contracted)

- 24/7 staffed SOC
- Unlimited IT helpdesk
- Guaranteed zero incidents
- Managing unrelated vendors without fee
- Emergency on-site work

## Related

- [client-profitability-scorecard.md](client-profitability-scorecard.md)
- [../governance/financial-action-policy.md](../governance/financial-action-policy.md)
- [techops-security-operations-lite.md](techops-security-operations-lite.md)
