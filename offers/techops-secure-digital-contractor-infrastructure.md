# Secure Digital Contractor Infrastructure

Electrical / field-contractor vertical overlay for TechOps (Linear **AIO-49**).

**Specialize, don’t fork:** controls inherit
[Security Infrastructure Standard v1](../architecture/security-infrastructure-standard-v1.md)
and the [Audit v2](techops-governance-security-infrastructure-audit-v2.md) pack.
This document is the audit module, reference architecture, and packaged offer
language for contractors (electrical first; adaptable to adjacent trades).

## SKU one-liner

> Harden identity, job documents, estimating files, subcontractor access, field
> devices, CRM/field-service integrations, and recovery — with OT/IoT treated as
> **discovery**, not an OT cybersecurity sale — so estimating, scheduling, and
> follow-up agents can run under Observe/Assist without owning the plant network.

## Threat & workflow context

| Surface | Why it matters |
|---|---|
| Field phones / tablets | Job photos, estimates, customer PII leave the office |
| M365 / Google Workspace | Shared drives of plans, bids, change orders |
| Estimating / project files | Competitive and contract-sensitive data |
| Subcontractor access | Temporary identities with standing privilege risk |
| Remote job-site access | VPN/RDP sprawl; shared credentials |
| CRM / field-service | Appointments, invoices, customer contact data |
| Backup / recovery | Ransomware on office + cloud files stops the company |
| Connected building / IoT / EV / energy | Adjacent OT — inventory only unless separately scoped |

## Audit module (overlay scores)

Score 0–3. Add to Audit v2 after A–K + M.

| # | Control | Emphasize | SIS / audit map |
|---|---|---|---|
| EC1 | Field device MDM / encryption for company data | Phones/tablets accessing mail + files | SIS-DV-*, B |
| EC2 | Job-doc & estimate libraries least-privilege | Not “everyone in SharePoint” | SIS-DA-*, E |
| EC3 | Subcontractor accounts time-bound + reviewed | Guest/external sharing defaults | SIS-CL-02, SIS-SA-02 |
| EC4 | Remote admin path owned (VPN/ZTNA); no shared jump box passwords | SIS-NW-02 | C |
| EC5 | Field-service / CRM integrations inventoried with owners | SIS-AU-01, SIS-SA-01 | G/H |
| EC6 | AP/AR and payment-change dual control | Human gates | SIS-AG-04 |
| EC7 | Backup covers identity + files + line-of-business; restore tested | SIS-BK-* | F |
| EC8 | Connected equipment / IoT adjacency inventoried; explicit out-of-scope decision | Domain L | L1–L3 |
| EC9 | Agent/automation tiers for scheduling & follow-up declared (no unattended Execute on contracts/money) | SIS-AG-03/04 | I |
| EC10 | Procurement & project ops readiness (estimating, materials, suppliers, change orders, scheduling access) | Overlay | Discovery |

**Contractor overlay subtotal:** __ / 30

### Hard stops (contractor)

- EC1 = 0 and field staff handle customer data → cannot recommend Assist on mobile workflows.
- EC6 = 0 → no Execute on billing/payment automations.
- EC8 missing decision → do not imply OT protection in the proposal.

## Reference architecture

```text
Field device (MDM)
  → IdP + MFA (human)
  → Cloud files (classified job libraries)
  → Field-service / CRM (scoped integrations)
  → AION Gateway (agents) → policy → CRM / messaging APIs
  → Evidence + Continuous Assurance

OT / IoT / plant / EV chargers
  → Discovery inventory only (Audit domain L)
  → Separate scoped engagement if client requests
```

### Observe / Assist / Execute examples

| Workflow | Default tier | Gate |
|---|---|---|
| Read schedule / job status | Observe | — |
| Draft customer follow-up / estimate summary | Assist | Human send |
| Create appointment / CRM note (allow-listed) | Execute | Policy allow-list |
| Change order / contract / payment / refund | Human-gated | Named approver |
| Remote access to jobsite equipment | Out of agent scope | Ops / OT partner |

## Packaged offer

| Component | Includes | Excludes |
|---|---|---|
| **Audit module** | EC1–EC9 + standard A–K + M | OT pen-test / IEC plant assessments |
| **Foundation implementation** | Identity, devices, cloud sharing, backup, credential inventory | Full SOC |
| **Secure AI Execution** | Registry, gateway path for CRM/messaging agents, human gates | Unattended contract signing |
| **Managed / CA** | Monthly drift + backup freshness + exception review | 24/7 OT monitoring |

**Prerequisites:** willing executive owner; cloud identity admin access; inventory of field devices and subcontractors.

**Upgrade path:** Audit v2 → Foundation → Assist agents on follow-up → Continuous Assurance → (optional) separate OT phase.

## Sales language

| Theme | Client language |
|---|---|
| Secure Digital Contractor Infrastructure | Make the office + field safe enough for AI to help win and run jobs |
| Job docs & estimates | Least privilege; subcontractors get time-bound access |
| Field devices | Company data stays on managed, encrypted devices |
| Automation with brakes | Scheduling help yes; money and contracts stay human-gated |
| Connected equipment | We inventory adjacency; we do not pretend to be your OT firm |

### Commercial discovery (Oct 26, 2026)

Lead with **project-system reliability** and **procurement automation**
(quote prep, material availability, project updates, purchasing approvals) —
not industrial cybersecurity services before delivery expertise exists.
AI data-center demand is tightening electrical supply chains; inaccurate
estimates and weak scheduling become more expensive under constraint.
EC10 scores discovery maturity for that conversation.

## Acceptance (AIO-49)

| Criterion | Evidence |
|---|---|
| Audit module | EC1–EC10 table |
| Reference architecture | Architecture + tier examples |
| Packaged offer | Offer table + exclusions |
| OT/IoT as discovery only | EC8 + Domain L linkage |

## Related

- Audit v2: [techops-governance-security-infrastructure-audit-v2.md](techops-governance-security-infrastructure-audit-v2.md)
- Action tiers: [../governance/action-tiers.md](../governance/action-tiers.md)
- Pilot adjacency: electrical design partners may also run Revenue Capture OS — security overlay still required before Execute agents.
