# Secure Automation Deployment Standard (SA-STD-001)

**Version:** `1.0.0`  
**Applies to:** Implementation Engine deliveries on `execution-platform-v0.2.x`  
**First workflow:** Lead-to-Appointment v1 (`lead-to-appointment-v1`)

This is a **versioned deployment profile**, not a second runtime. It extends
IE-001/IE-002 by binding Revenue OS blueprints and Operator Console missions to
existing Agent OS controls and canonical records.

---

## Doctrine

| Rule | Meaning |
|---|---|
| Reuse Agent OS | Tenant, capability, policy, approval, run, side-effect, outcome — no parallel control plane |
| Commercial ≠ technical | Paid/signed still requires IE-002 activation |
| Communication default deny | Customer **send** is out of L2A v1; draft-only with approval |
| Human books appointments | `crm.appointment.*` catalog stubs are **inactive** until AIO-17 |
| Research ≠ claims | Offer-catalog / brief vendor & regulatory statements stay unverified until evidence |

---

## Required controls

| Control | Agent OS mechanism | Canonical record |
|---|---|---|
| Tenant isolation | `x-aion-tenant-id` + Actor/Scope | `ImplementationCase.tenantId`, `Run.tenantId`, `ExternalSideEffect.tenantId` |
| Scoped permissions | Capability + Policy + ServiceDefinition | Mission permissions / Service catalog |
| Approved communication | Blueprint prohibitions + R2 draft / R3 send | `SolutionBlueprint.prohibitedActions`, Approval |
| Durable execution | Run/Execution lifecycle | Run / Execution / Event |
| Duplicate prevention | Side-effect idempotency + IE-002 reopen rules | `ExternalSideEffect.idempotencyKey` |
| Auditability | Telemetry + Events + Approvals + side-effect ledger | Event / Telemetry / Approval / ExternalSideEffect |
| Outcome measurement | Blueprint KPI + Outcome + Economics | Outcome / Economics |

Code source of truth: `@aion/core` → `secure-automation.ts`
(`getSecureAutomationStandard()`).

---

## Lead-to-Appointment v1 (operable path)

```text
lead
  → research (revenue.lead.research)
  → enrich (revenue.lead.enrich)
  → opportunity (crm.opportunity.create, approval)
  → task (crm.task.create) — appointment-setting work item
  → note (crm.note.create)
  → draft message (crm.message.draft, approval)  ← NO send
  → human books appointment in CRM calendar
  → outcome + cost units
```

**Prohibited in v1:** `crm.message.send`, autonomous customer messaging,
automatic workflow activation without acceptance.

**Reserved (inactive):** `crm.appointment.read|create|update@1` via
`buildSecureAutomationAppointmentCatalog()` — not wired to adapters.

### Implementation Engine wiring

- Package `revenue_os` blueprints drafted by `draftBlueprintFromCase` attach
  `secureAutomation` profile `SA-STD-001` / `lead-to-appointment-v1`.
- Console: Implementation detail shows standard + workflow ids when present.
- Console: New Mission template **Lead-to-Appointment v1** (alongside Revenue
  Production v1). Human-operated booking steps are metadata only — not submitted
  as executable Runtime steps.

---

## OL-001–005 verification map

| OL | Status for SA L2A | Evidence still needed |
|---|---|---|
| **OL-001** Revenue Production | **blocked** | Live GHL + model access; supervised missions with outcome/cost; do **not** count IE work as missions |
| **OL-002** Client Production | **not_started** | Deferred until OL-001 baseline; then one client tenant under SA-STD-001 |
| **OL-003** Unit economics | **partial** | M005 contracts exist; need L2A cost + human-minutes evidence |
| **OL-004** Workforce optimization | **not_started** | Needs production volume for M007 scorecards |
| **OL-005** Productization | **partial** | Standard + Console template shipped; live productization waits on activation |

---

## Remaining production blockers

| Id | Severity | Blocks |
|---|---|---|
| **AIO-16** | P0 | Model provider access / `model_access` gate |
| **AIO-17** | P0 | Live GHL (beyond config-presence probes) + calendar appointment services |
| **SEND-GATE** | P0 | Any client claim of autonomous messaging / `crm.message.send` |
| **WORKFLOW-ACTIVATION** | P0 | Unattended `deliveryStatus=active` without human activate |
| **OL-001-COUNT** | P1 | Premature “100 missions” credit from IE activity |
| **CLAIM-VERIFY** | P1 | Client-facing vendor / regulatory / guaranteed-outcome language |

IE-002 probes remain **readiness evidence only** — they do not auto-provision.

---

## Research caveats (non-claims)

Treat the following as **unverified research inputs** until primary evidence exists:

1. **Offer catalog** Lead → Appointment → Customer commercial language and
   “AI appointment setter” positioning.
2. **External brief** vendor capability / certification statements.
3. **Regulatory** implications (TCPA, CAN-SPAM, GDPR, industry rules) before
   enabling any send path.

Do **not** put these in client-facing guarantees from this standard alone.

---

## Related docs

- [IE-001 Implementation Engine](./ie-001-implementation-engine.md)
- [IE-002 Provisioning + Activation](./ie-002-provisioning-activation.md)
- [Operating leverage (OL-001–005)](./operating-leverage.md)

## Explicit non-goals

- Not IE-003
- Not completion of OL-001
- Not live calendar automation
- Not enabling `crm.message.send` by default
