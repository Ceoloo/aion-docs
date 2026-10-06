# AION Security Infrastructure Standard v1

- **Status:** Active (AIO-43)
- **Version:** 1.0
- **Date:** 2026-10-05
- **Owners:** TechOps / Security Infrastructure Layer
- **Applies to:** Every AION internal environment and every client deployment
  that inherits the TechOps baseline
- **Source brief:** TechOps Monday Brief — Oct 5, 2026 (Blueprint Alliance /
  Google Agent Gateway / NIST token guidance / agentic SecOps)

This is the **canonical control baseline**. Architectural intent lives in
[security-model.md](security-model.md); agent behavior rules live in
[../governance/agent-governance.md](../governance/agent-governance.md).
This standard answers: **what must be true**, **how it is verified**, and
**what is mandatory vs recommended**.

Commercial packaging of this standard is the three-layer TechOps ladder:

1. **Secure Digital Foundation**
2. **Secure AI Execution**
3. **Continuous Assurance**

Vertical overlays (contractor / accounting / automotive) specialize controls;
they do **not** create separate architectures. See
[../offers/README.md](../offers/README.md).

---

## 1. Operating doctrine

```text
unique agent identity
  → scoped authority
  → gateway enforcement
  → runtime monitoring
  → rapid containment
```

Industry signal (Blueprint Alliance, Google Cloud Agent Gateway, NIST SP 800-63
token updates) converges on the same questions TechOps already asks:

| Question | Control surface |
|---|---|
| Where are my agents? | Agent Identity Registry (AIO-44) |
| What can they do? | Permission tier + tools + data scope + delegated authority |
| What are they doing? | Execution evidence + Continuous Assurance |
| How do I stop them? | Revocation state + kill / contain |

**Hard architectural rule:** sensitive execution crosses the **AION Action /
Execution Gateway** — never `agent → raw API key → business API`.

```text
Agent → AION Gateway → Policy → Business API
```

Raw CRM, accounting, DMS, email, or cloud credentials must progressively
disappear from agent runtimes (credential isolation; short-lived / vaulted /
delegated credentials where supported). Full gateway contract: AIO-45.

---

## 2. Control record format

Every control uses this shape:

| Field | Meaning |
|---|---|
| **ID** | Stable identifier (`SIS-*`) |
| **Title** | Short name |
| **Level** | `M` = mandatory · `R` = recommended |
| **Audit map** | Scorecard row(s) in [../offers/techops-security-agent-readiness-audit.md](../offers/techops-security-agent-readiness-audit.md) |
| **Evidence** | What proves the control is true |
| **Owner** | Who keeps it true (role / system) |

Unknown or unverified controls score **0** on the audit (fail closed).

---

## 3. Control catalog

### 3.1 Human identity (`SIS-HI-*`)

| ID | Title | Lvl | Audit | Evidence |
|---|---|---|---|---|
| SIS-HI-01 | Phishing-resistant MFA for admin + finance | M | A1 | IdP policy export; privileged sign-in without compliant MFA fails |
| SIS-HI-02 | MFA enforced for all workforce users | M | A2 | Conditional access / org MFA enforcement screenshot + sample |
| SIS-HI-03 | OAuth / app-consent restricted | M | A3 | User consent blocked; admin approval required |
| SIS-HI-04 | Privileged accounts separated | M | A4 | No shared mailbox as standing identity for privileged work |
| SIS-HI-05 | Suspicious-session response playbook | M | A5 | Documented revoke path (IdP + mail + SaaS) with owner + RTO |

### 3.2 Device / endpoint (`SIS-DV-*`)

| ID | Title | Lvl | Audit | Evidence |
|---|---|---|---|---|
| SIS-DV-01 | Endpoint protection on workstations / servers | M | B1 | Coverage inventory ≥ declared estate |
| SIS-DV-02 | Device compliance / MDM for company data | M | B2 | Non-compliant device blocked from mail/files |
| SIS-DV-03 | Disk encryption + screen lock | M | B3 | Policy + sample device attestation |
| SIS-DV-04 | Local admin removed for standard users | R | B4 | MDM / GPO evidence |

### 3.3 Network (`SIS-NW-*`)

| ID | Title | Lvl | Audit | Evidence |
|---|---|---|---|---|
| SIS-NW-01 | Guest / IoT segmented from corporate | R | C1 | Network diagram + ACL evidence |
| SIS-NW-02 | VPN or ZTNA for remote admin | M | C2 | Admin path requires ZTNA/VPN |
| SIS-NW-03 | DNS / web filtering | R | C3 | Policy + block sample |
| SIS-NW-04 | Firewall change ownership + logging | M | C4 | Change log + owner |

### 3.4 Cloud / SaaS workspace (`SIS-CL-*` / `SIS-SA-*`)

| ID | Title | Lvl | Audit | Evidence |
|---|---|---|---|---|
| SIS-CL-01 | Tenant hardening baseline | M | D1 | Baseline checklist completed |
| SIS-CL-02 | External sharing least privilege | M | D2 | Default sharing restricted |
| SIS-CL-03 | Admin roles JIT / limited standing privilege | R | D3 | Standing global admin count justified |
| SIS-CL-04 | Audit logs retained ≥ 180 days | M | D4 | Retention setting + sample query |
| SIS-SA-01 | SaaS inventory with business data | M | G1 | Owned inventory |
| SIS-SA-02 | Offboarding revokes SaaS ≤ 24h | M | G2 | Last offboard evidence |
| SIS-SA-03 | Shadow AI discovery + acceptable-use policy | M | G3 | Policy + discovery method |
| SIS-SA-04 | Vendor / integration review cadence | R | G4 | Quarterly review record |

### 3.5 Data (`SIS-DA-*`)

| ID | Title | Lvl | Audit | Evidence |
|---|---|---|---|---|
| SIS-DA-01 | Data classes identified | M | E1 | Classification table |
| SIS-DA-02 | Access follows least privilege | M | E2 | Sample share audit |
| SIS-DA-03 | Regulated data locations mapped | M | E3 | Location map (PII / tax / financing / payment) |
| SIS-DA-04 | DLP or equivalent on mail + files | R | E4 | Policy + hit sample |

### 3.6 Backup / recovery (`SIS-BK-*`)

| ID | Title | Lvl | Audit | Evidence |
|---|---|---|---|---|
| SIS-BK-01 | Backups for identity + cloud files + LoB | M | F1 | Backup job inventory |
| SIS-BK-02 | Immutable / offline copy | M | F2 | Immutability config |
| SIS-BK-03 | Restore tested ≤ 90 days | M | F3 | Restore test record |
| SIS-BK-04 | RPO/RTO documented and owned | M | F4 | Owned RPO/RTO doc |

### 3.7 Credential & token inventory (`SIS-CT-*`) — Human + Machine Identity

Category shift from the Oct 5 brief: TechOps sells **Human + Machine Identity
Security**, not password cleanup alone. SMB stacks concentrate exactly the
token classes NIST token guidance addresses (Zapier, n8n, CRM OAuth, Google /
Microsoft, Shopify, accounting, webhooks, MCP).

| ID | Title | Lvl | Audit | Evidence |
|---|---|---|---|---|
| SIS-CT-01 | Credential & Token Inventory exists | M | K1 | Inventory covering fields below |
| SIS-CT-02 | Every credential has owner + system + scope | M | K2 | No orphan rows |
| SIS-CT-03 | Expiration + storage location recorded | M | K3 | Inventory columns populated |
| SIS-CT-04 | Rotation mechanism documented | M | K4 | Runbook or automated rotation |
| SIS-CT-05 | Revocation mechanism documented | M | K5 | Tested revoke path |
| SIS-CT-06 | Ownership class labeled (human / service / agent) | M | K6 | Class column complete |
| SIS-CT-07 | Agent runtimes do not hold long-lived business API keys | M | K7 / I* | Vault / gateway / short-lived credential proof |

**Mandatory inventory columns**

| Column | Required |
|---|---|
| owner | yes |
| system | yes |
| scope | yes |
| expiration | yes (or “none — justify”) |
| storage_location | yes |
| rotation_mechanism | yes |
| revocation_mechanism | yes |
| ownership_class | `human` \| `service` \| `agent` |

### 3.8 Automation fabric (`SIS-AU-*`)

| ID | Title | Lvl | Audit | Evidence |
|---|---|---|---|---|
| SIS-AU-01 | Integrations inventory with owners | M | H1 | Owned list (Zapier/Make/n8n/native/MCP) |
| SIS-AU-02 | Secrets not in shared inboxes / sheets | M | H2 | Negative search + vault usage |
| SIS-AU-03 | Change control for production automations | M | H3 | Change record sample |
| SIS-AU-04 | Failure alerts + human owner | M | H4 | Alert → owner path |

### 3.9 Agent identity & governance (`SIS-AG-*`) — **required infrastructure**

Agent Identity Registry + Secure Execution Layer are **mandatory controls**,
not optional advanced features (Oct 5 brief; validates AIO-43/44/45).

Every production agent / non-human worker **must** carry these fields before
Execute-tier work is allowed:

| Field | Required | Notes |
|---|---|---|
| `agent_id` | M | Canonical attributable ID (`agent://aion/...` when on AION) |
| `human_owner` | M | Named accountable human |
| `business_purpose` | M | One-job purpose statement |
| `tenant` | M | Tenant binding; header is filter, not credential |
| `permission_tier` | M | Observe / Assist / Execute ([action-tiers.md](../governance/action-tiers.md)) |
| `tools` | M | Exact tool allow-list |
| `data_scope` | M | Allowed data classes / systems |
| `delegated_authority` | M | Traceable delegation from human/service principal |
| `policy_version` | M | Policy bundle / version governing the agent |
| `execution_evidence` | M | Link/path to attributable run / audit records |
| `revocation_state` | M | `active` \| `suspended` \| `revoked` (+ contain path) |

| ID | Title | Lvl | Audit | Evidence |
|---|---|---|---|---|
| SIS-AG-01 | Distinct agent identities (not shared user logins) | M | I1 | Registry rows ≠ shared mailbox/user |
| SIS-AG-02 | Mandatory registry fields complete | M | I1–I4 | Schema validation / inventory export |
| SIS-AG-03 | Observe / Assist / Execute declared per workflow | M | I2 | Tier on registry + workflow |
| SIS-AG-04 | Human gates on financial / destructive / regulated | M | I3 | Gate config + sample approvals |
| SIS-AG-05 | Permissions + data-class allow-lists enforced | M | I4 | Fail-closed policy proof (not prompt-only) |
| SIS-AG-06 | Execution Trust Score / audit trail for operators | M | I5 | Operator-visible evidence ([agent-trust-score.md](../governance/agent-trust-score.md)) |
| SIS-AG-07 | Delegated authority recorded and traceable | M | I* / K* | Delegation record on registry |
| SIS-AG-08 | Policy version pinned on agent registration | M | I* | `policy_version` field |
| SIS-AG-09 | Revocation / contain path tested | M | I* / A5 | Tabletop or live revoke evidence |
| SIS-AG-10 | Orphaned / unknown agents fail governance review | M | I1 | Review finding or CI gate |

### 3.10 Secure AI Execution / Gateway (`SIS-GX-*`)

| ID | Title | Lvl | Audit | Evidence |
|---|---|---|---|---|
| SIS-GX-01 | Authenticated principal before authorization | M | I* / platform | Gateway authn proof |
| SIS-GX-02 | Tenant binding enforced | M | I* | Cross-tenant attempt denied |
| SIS-GX-03 | Tool allow-lists enforced at gateway | M | I4 | Deny outside allow-list |
| SIS-GX-04 | Tier policy enforced at gateway | M | I2 | Observe cannot mutate |
| SIS-GX-05 | Data-scope enforced at gateway | M | I4 / E* | Class violation denied |
| SIS-GX-06 | Human approval for sensitive actions | M | I3 | R3 / financial path gated |
| SIS-GX-07 | Short-lived / vaulted credentials; no raw LoB keys in runtime | M | K7 | Secrets architecture + negative scan |
| SIS-GX-08 | Full execution audit evidence | M | J3 / I5 | Execution records |
| SIS-GX-09 | Rate / spend limits | R | J4 | Limit config |
| SIS-GX-10 | Emergency revoke + kill / contain | M | I* / A5 | Kill-switch evidence |

Normative product path remains Runtime Execution Gateway
([ADR-003](../adr/ADR-003-execution-gateway-into-runtime.md)). AIO-45 owns the
detailed Secure Execution Layer contract and threat model.

### 3.11 Observability & incident response (`SIS-OB-*` / `SIS-IR-*`)

| ID | Title | Lvl | Audit | Evidence |
|---|---|---|---|---|
| SIS-OB-01 | Centralized auth / mail / endpoint alerts reviewed | M | J1 | Review cadence evidence |
| SIS-OB-02 | Incident record + owner notification path | M | J2 | Sample incident |
| SIS-OB-03 | Automation/agent runs attributable | M | J3 | who/what/when/tenant |
| SIS-OB-04 | Cost / usage visibility for AI + integrations | R | J4 | Usage report |
| SIS-IR-01 | Named incident owner per open item | M | J2 | Incident record |
| SIS-IR-02 | Containment includes human + non-human identities | M | A5 / AG-09 | Revoke sessions + disable agent |

### 3.12 Continuous Assurance (`SIS-CA-*`)

Continuous Assurance is designed around **connectors and evidence**, not humans
manually reviewing dashboards (Oct 5 brief → AIO-47 commercial model).

Loop:

```text
Observe → investigate → recommend → human approve → remediate → verify
```

| ID | Title | Lvl | Audit | Evidence |
|---|---|---|---|---|
| SIS-CA-01 | Assurance check catalog defined | M | (AIO-47 / Audit v2) | Check catalog doc |
| SIS-CA-02 | Connector-sourced evidence preferred over screenshots | M | Invariants | Evidence links |
| SIS-CA-03 | Drift domains covered | M | — | Exposure, identity, permission, vuln, backup, agent behavior |
| SIS-CA-04 | Human gate on remediation of high impact | M | I3 | Approval records |
| SIS-CA-05 | Monthly client-facing assurance report | R | — | Report template |

Initial check domains (AIO-47): external exposure · identity posture ·
credential/secrets hygiene · cloud/SaaS drift · backup freshness + restore ·
agent permission drift · runtime/endpoint health · vulnerability signals ·
kill-switch / revocation readiness.

---

## 4. Mapping: TechOps layers → controls

| Layer | Primary control families | Client SKU today |
|---|---|---|
| **1 — Secure Digital Foundation** | HI, DV, NW, CL, SA, DA, BK, CT (human/service) | [AI-Era Identity Protection](../offers/techops-ai-era-identity-protection.md) + foundation hardening |
| **2 — Secure AI Execution** | AG, GX, AU, CT (agent), DA (scope) | Secure Automation / gateway path (AIO-44/45) |
| **3 — Continuous Assurance** | CA, OB, IR | [Security Operations Lite](../offers/techops-security-operations-lite.md) → Continuous Assurance (AIO-47) |

---

## 5. Vertical overlays (specialize, don’t fork)

### 5.1 Accounting — strongest near-term security wedge

Offer language: **Secure Accounting AI Foundation**

Emphasize before meaningful automation: identity/MFA · secure document flow ·
AI acceptable-use · machine/agent identity inventory · financial-action approval
gates · logging · recovery.

Overlay intensifies: SIS-HI-*, SIS-DA-03/04, SIS-AG-04/06, SIS-GX-06/07,
SIS-BK-*, SIS-CT-*.

### 5.2 Auto dealers / rentals — governance over “AI”

FTC Safeguards Rule / financial-institution posture for motor vehicle dealers.
Message: automate aggressively without unrestricted AI authority over customer
data, pricing, financing, or contracts.

**Authority bands (dealer reference architecture):**

| Band | Examples | Default posture |
|---|---|---|
| 1 — Inventory / search | Stock search, approved catalog read | Observe |
| 2 — Communication | Draft follow-up, recommend offers | Assist |
| 3 — CRM operations | Appointments, CRM updates (allow-listed) | Execute (policy) |
| 4 — Financial / contract | Financing submit, refunds, voids, contracts | **Human-gated** |

### 5.3 Electrical contractors — Secure Digital Contractor Infrastructure

Position current offer as **Secure Digital Contractor Infrastructure**.
Treat OT/IoT / connected infrastructure as an **audit discovery category**,
not an implementation promise (do not sell OT cybersecurity services yet).

---

## 6. Internal AION compliance gap list (customer-zero)

Snapshot for AIO-46. Status meanings: **Met** (evidenced) · **Partial** ·
**Gap** (not yet production-proven under this standard).

| Area | Status | Notes / follow-on |
|---|---|---|
| Human MFA / IdP hardening for AION ops | Partial | Documented practices exist; customer-zero score pending AIO-46 |
| Secrets not in git | Met | Hard rule + CI hygiene; continuous scan still required |
| Execution Gateway as Runtime ingress | Partial | ADR-003 + Runtime routes; product paths still migrating |
| Agent URI / identity contracts | Partial | `aion-core` agent identity URI; Registry schema + API = AIO-44 |
| Mandatory registry fields (tenant, delegated_authority, policy_version, execution_evidence, revocation_state) | Partial | AIO-44 schema/API/inventory landed; production registration still required per environment |
| Observe / Assist / Execute | Partial | ADR-007 + governance docs; enforce everywhere via AIO-45 |
| Credential & Token Inventory (human/service/agent) | Gap | Add to AION ops runbooks; audit domain K (AIO-46) |
| No long-lived LoB API keys in agent runtimes | Partial | Doctrine clear; progressive credential isolation under AIO-45 |
| Kill / revoke / contain for agent identities | Partial | Registry revoke/suspend + FeatureGate; full contain path in AIO-45 |
| Continuous Assurance connectors + evidence loop | Partial | AIO-47 catalog + evidence schema; connectors follow-on |
| Backup / DR evidence for AION itself | Partial | Related AIO-7; restore evidence required for Met |
| Customer-zero scored audit | Partial | AIO-46 scored baseline |

**Build order (do not expand scope):**  
AIO-43 (this standard) → AIO-44 (Registry) → AIO-45 (Secure Execution Layer) →
AIO-46 (customer-zero audit).

---

## 7. Acceptance (AIO-43)

| Criterion | Evidence |
|---|---|
| One versioned standard in aion-docs | This document (`v1.0`) |
| Control IDs map to TechOps audit scorecard | §3 Audit columns + §4; audit domains A–K |
| Mandatory vs recommended clear | `M` / `R` on every control |
| Internal AION compliance gap list | §6 |

---

## 8. Related work

| Artifact | Relationship |
|---|---|
| [security-model.md](security-model.md) | Architectural security model |
| [../governance/agent-governance.md](../governance/agent-governance.md) | Agent specification fields |
| [../governance/action-tiers.md](../governance/action-tiers.md) | Observe / Assist / Execute |
| [../adr/ADR-003-execution-gateway-into-runtime.md](../adr/ADR-003-execution-gateway-into-runtime.md) | Gateway ownership |
| [../offers/techops-security-agent-readiness-audit.md](../offers/techops-security-agent-readiness-audit.md) | Client audit scorecard |
| Linear AIO-43 … AIO-52 | Execution tracker |
| Notion: TechOps Security Infrastructure Layer — Development Plan (Sep 29, 2026) | Program strategy |

## 9. Versioning

- Breaking control removals or ID renames require a new major version (`v2`).
- Additive controls may ship as `v1.x` with changelog in PR description.
- Client audits always declare which standard version they were scored against.
