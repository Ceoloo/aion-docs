# Governance & Security Infrastructure Audit v2

- **Status:** Active (AIO-48)
- **Date:** 2026-10-23
- **Supersedes for delivery packaging:** v1 front-door naming only —
  scoring still uses [techops-security-agent-readiness-audit.md](techops-security-agent-readiness-audit.md)
  domains A–K (+ L discovery). This document adds governance depth,
  deliverable formats, and offer mapping required for client engagements.
- **Baseline:** [Security Infrastructure Standard v1](../architecture/security-infrastructure-standard-v1.md)

Evolve the Security & Agent Readiness Audit beyond revenue/automation readiness
into a combined **business + security + governance** assessment that maps cleanly
to the TechOps ladder and vertical overlays.

## What v2 adds

| Area | v1 | v2 |
|---|---|---|
| Scorecard A–K (+ L) | Yes | Retained unchanged |
| Governance ownership / policy maturity | Light | Domain **M** + exec narrative |
| Human / service / agent identity inventory | Domains A, I, K | Explicit inventory appendix |
| Secrets & credential lifecycle | Domain K | Lifecycle checklist + exceptions |
| AI acceptable-use / model-data policy | G3 / I\* | Policy maturity rubric |
| Vendor / SaaS risk | G | Vendor risk table |
| Incident response | J2 | IR maturity + revoke path |
| Business continuity / DR | F | BC/DR evidence requirements |
| Continuous assurance requirements | Implied | Cadence + report expectation |
| Vertical compliance overlays | Notes | Mandatory overlay selection |
| Client deliverables | Outline | Full templates below |

## Domain M — Governance ownership (v2 add-on)

Score 0–3 each. Reported separately from the A–K /141 total; still required
for any Execute-tier recommendation.

| # | Control | Evidence | SIS / related |
|---|---|---|---|
| M1 | Named executive owner for security + AI risk | Org chart / RACI | SIS owners |
| M2 | Written AI / automation acceptable-use policy | Dated policy | SIS-SA-03 |
| M3 | Exception register (risk accepted, owner, review date) | Living register | Continuous Assurance |
| M4 | Change ownership for production automations/agents | Tickets / ADRs | SIS-AU-03 |
| M5 | Kill/revoke authority documented and tested | Tabletop or drill note | SIS-AG / GX |
| M6 | Vendor/SaaS critical list with renewal & access review cadence | Inventory + last review | SIS-SA-01/04 |

**Governance subtotal:** __ / 18

### Governance maturity bands

| Band | Range | Meaning |
|---|---|---|
| Ad hoc | 0–5 | Policy tribal; do not sell managed autonomy |
| Defined | 6–11 | Policy exists; enforcement uneven |
| Managed | 12–15 | Owners + exceptions + reviews |
| Assured | 16–18 | Evidence loop + Continuous Assurance candidate |

## Overlay selection (required)

Every v2 engagement selects **one primary vertical overlay** (specialize, don’t fork):

| Vertical | Overlay artifact |
|---|---|
| Electrical / field contractors | [Secure Digital Contractor Infrastructure](techops-secure-digital-contractor-infrastructure.md) |
| Accounting / CPA | [Secure Accounting AI Foundation](techops-secure-accounting-ai-foundation.md) + [governance policy template](techops-accounting-ai-governance-policy-template.md) |
| Auto dealer / rental | [Dealer/Rental security & pricing governance](techops-dealer-rental-security-overlay.md) |

If the client spans verticals, score the primary overlay fully and note secondary
controls as “next engagement.”

## Client deliverable pack (acceptance)

### 1. v2 scorecard

Fill:

1. Domains **A–K** from [techops-security-agent-readiness-audit.md](techops-security-agent-readiness-audit.md)
2. Domain **L** when contractor / OT adjacency is in scope (informational)
3. Domain **M** (this document)
4. Automation-readiness band + hard stops
5. Overlay-specific module scores from the selected vertical doc

Declare the SIS version scored against (currently `v1.0`).

### 2. Executive summary format

```markdown
# [Client] — Governance & Security Infrastructure Audit v2
Date · SIS version · Auditor · Overlay

## Bottom line (≤5 sentences)
Automation readiness band · hard stops · top business risk

## Score snapshot
| Metric | Value |
| A–K total | __ / 141 |
| Automation readiness | __ / 69 → band |
| Governance (M) | __ / 18 → band |
| Overlay module | __ (see appendix) |

## Why it matters commercially
What they cannot safely automate yet; what becomes sellable after 30/60/90

## Recommended TechOps path
Foundation / Secure AI Execution / Continuous Assurance / Vertical add-on

## Decisions needed from the client this week
```

### 3. Technical findings appendix

For each finding (top 10 minimum):

| Field | Content |
|---|---|
| ID | `F-01` … |
| Domain | A–M / overlay |
| Blast radius | Identity / money / PII / availability / agent Execute |
| Current state | Evidence-backed |
| Target state | Control language from SIS |
| Effort | S / M / L |
| Maps to offer | Foundation / Secure AI Execution / CA / Overlay |

Unknown evidence scores **0** (fail closed). Screenshots of tool logos are not evidence.

### 4. 30 / 60 / 90 roadmap template

| Horizon | Outcomes | Typical work |
|---|---|---|
| **30 days** | Hard stops cleared or risk-accepted with owner | MFA/passkeys, credential inventory start, backup ownership, AI acceptable-use draft, agent registry seed |
| **60 days** | Assist-ready or Execute-gated path clear | Document boundaries, gateway-mediated writes, human gates on money/contracts, restore test |
| **90 days** | Managed path or Continuous Assurance start | Exception register cadence, Trust Score / evidence path, overlay-specific managed SKU |

Every roadmap item names: **owner**, **evidence artifact**, **offer layer**.

### 5. Offer-mapping section

| Finding cluster | Layer | Primary SKU / doc |
|---|---|---|
| MFA, devices, cloud, backup, secrets | 1 — Secure Digital Foundation | [AI-Era Identity Protection](techops-ai-era-identity-protection.md) |
| Agent registry, tiers, gateway, approvals | 2 — Secure AI Execution | SIS + Secure Execution Layer |
| Drift, alerts, restore freshness, exceptions | 3 — Continuous Assurance | [Security Operations Lite](techops-security-operations-lite.md) → [continuous-assurance-v1.md](../architecture/continuous-assurance-v1.md) |
| Vertical intensifiers | Add-on | Contractor / Accounting / Dealer overlays |

Revenue/process opportunity (from AIO-57 retained scope) is captured in the
executive summary “commercially” block and may reference Outcome-Based Services
or Pilot-to-Revenue — it does **not** replace security scoring.

## Engagement invariants

- Observe / Assist / Execute must be declared per workflow before Execute sales.
- Human approval gates required for financial, destructive, and regulated actions.
- Agent Identity Registry fields are mandatory for any production agent (SIS-AG).
- Continuous Assurance is sold as verification that the baseline **stays** true.
- OT/IoT for contractors remains discovery unless a separate scoped engagement exists.

## Acceptance (AIO-48)

| Criterion | Evidence |
|---|---|
| v2 scorecard | Domains A–K + M (+ L when applicable) defined above |
| Executive summary format | §2 template |
| Technical findings appendix | §3 schema |
| 30/60/90 roadmap template | §4 |
| Offer-mapping section | §5 |
| Vertical overlays linked | Overlay selection table |

## Related

- Front-door scorecard: [techops-security-agent-readiness-audit.md](techops-security-agent-readiness-audit.md)
- Customer-zero instance: [aion-customer-zero-security-baseline-2026-10-06.md](aion-customer-zero-security-baseline-2026-10-06.md)
- Offer ladder packaging (follow-on): Linear AIO-52
