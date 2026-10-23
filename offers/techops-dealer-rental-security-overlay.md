# Dealer / Rental Security & Pricing Governance Overlay

Automotive dealer and rental vertical overlay for TechOps (Linear **AIO-51**).

**Specialize, don’t fork:** inherits
[Security Infrastructure Standard v1](../architecture/security-infrastructure-standard-v1.md)
and [Audit v2](techops-governance-security-infrastructure-audit-v2.md).
Primary commercial message: automate aggressively **without** unrestricted AI
authority over customer data, pricing, financing, or contracts.

## SKU one-liner

> Protect customer PII and financing/lease data; bind CRM/DMS access to
> Observe / Assist / Execute tiers; enforce a **canonical pricing source** across
> website, ads, CRM, SMS, and agents; keep finance, refunds, contracts, and
> destructive actions human-gated — with incident and revocation controls.

## Threat & workflow context

| Surface | Why it matters |
|---|---|
| Customer PII / credit apps | Regulated and high blast-radius data |
| Financing / lease systems | Money + legal liability |
| CRM / DMS | System of record for inventory, deals, service |
| Website / ads / SMS pricing | Inconsistent prices destroy trust and create agent sprawl |
| Endpoint / network | Desk, F&I, and service lanes share blast radius |
| Agents / automations | Unscoped Execute can discount, message, or mutate deals |

## Authority bands (reference architecture)

| Band | Scope | Default posture | Examples |
|---|---|---|---|
| **1** | Inventory / search | Observe | Stock search, approved catalog read |
| **2** | Communication | Assist | Draft follow-up; recommend offers |
| **3** | CRM operations | Execute (allow-listed) | Appointments, CRM updates |
| **4** | Financial / contract | **Human-gated** | Financing submit, refunds, voids, contracts, price overrides |

Pricing policy: agents and automations may only surface prices from the
**canonical pricing source**. Website, ads, CRM, and SMS must not invent
independent price authority.

## Audit module (overlay scores)

Score 0–3. Add to Audit v2 after A–K + M.

| # | Control | Emphasize | SIS / audit map |
|---|---|---|---|
| DR1 | Customer PII + financing data locations mapped | SIS-DA-03 | E |
| DR2 | CRM/DMS roles least-privilege; no shared F&I logins | SIS-HI-04, SIS-CL-03 | A/D |
| DR3 | Observe/Assist/Execute declared per dealer workflow | SIS-AG-03 | I2 |
| DR4 | Canonical pricing source named; non-canonical channels read-only or synced | Policy + architecture | Overlay |
| DR5 | Price override / discount requires human gate + evidence | SIS-AG-04, SIS-GX-06 | I3 |
| DR6 | Band-4 actions (finance, refund, void, contract) cannot be agent-Execute | SIS-AG-04 | I3 |
| DR7 | Website/ads/CRM/SMS pricing reconciliation ownership | Ops RACI | Overlay |
| DR8 | Endpoint + network segmentation for F&I / server rooms | SIS-DV-*, SIS-NW-* | B/C |
| DR9 | Incident + revoke path for agent and integration credentials | SIS-CT-*, SIS-AG | J/K |
| DR10 | Backup/restore covers DMS/CRM exports or vendor RPO evidence | SIS-BK-* | F |

**Dealer/rental overlay subtotal:** __ / 30

### Hard stops (dealer/rental)

- DR1 = 0 or DR6 = 0 → cannot be Execute-ready.
- DR4 = 0 → do not sell pricing agents or “AI desk” Execute.
- Shared F&I credentials (DR2 = 0) → Foundation first.

## Reference architecture

```text
Canonical pricing source
  → Website / ads / CRM / SMS (consumers, not authorities)
  → AION Gateway
       → Band 1–3 policies
       → Band 4 requires human approval (exact payload)
  → DMS / CRM / messaging APIs
  → Immutable evidence + Continuous Assurance

Identity: human MFA + agent registry (no shared F&I users as agent identity)
```

## Packaged offer

| Component | Includes | Excludes |
|---|---|---|
| **Audit module** | DR1–DR10 + A–K + M | Full DMS vendor replacement |
| **Foundation** | Identity, devices, data map, backup evidence | PCI-DSS QSA engagement |
| **Secure AI Execution** | Authority bands, gateway, pricing policy enforcement, approvals | Unattended financing submit |
| **Managed security / automation** | Drift on identities, pricing source integrity checks, revoke drills | 24/7 SOC for OEM networks |

**Prerequisites:** executive sponsor (GM / ops); CRM/DMS admin access; named
pricing owner; willingness to human-gate Band 4.

**Upgrade path:** Audit v2 → Foundation → Band 1–2 Assist → Band 3 allow-lists →
Continuous Assurance. Band 4 stays human-gated.

## Sales language

| Theme | Client language |
|---|---|
| Governance over “AI” | Automate the desk without giving AI the deal jacket |
| Canonical pricing | One price authority — site, ads, CRM, and agents agree |
| Authority bands | Search and follow-up help; money and contracts need a human |
| Customer trust | PII and credit data stay scoped and attributable |
| Kill switch | Every agent and integration can be revoked fast |

## Acceptance (AIO-51)

| Criterion | Evidence |
|---|---|
| Audit module | DR1–DR10 |
| Reference architecture | Authority bands + pricing source |
| Managed offer packaging | Offer table |
| Human gates on finance/contracts | DR5/DR6 hard stops |

## Related

- Audit v2: [techops-governance-security-infrastructure-audit-v2.md](techops-governance-security-infrastructure-audit-v2.md)
- Action tiers (dealer examples): [../governance/action-tiers.md](../governance/action-tiers.md)
- SIS §5.2 dealer doctrine: [../architecture/security-infrastructure-standard-v1.md](../architecture/security-infrastructure-standard-v1.md)
