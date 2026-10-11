# Demo-client workflow — AION Empire (customer-zero)

**Status:** Locked design for Sprint 1 (2026-10-12)  
**Implementation:** Sprint 2 (Oct 19–20)  
**Process contract:** [process-contract.md](process-contract.md) Part B  
**Capabilities:** [ghl-capabilities.md](ghl-capabilities.md)

---

## 1. Goal

Run one complete **governed** inquiry → qualify → prepare booking → human book →
verify → Outcome path against the AION Empire GHL tenant, without autonomous
send or appointment API writes.

Success for the demo design = every step has an owner, a capability, a gate, and
a verifiable artifact. Success for Sprint 2 = those artifacts exist in Runtime /
CRM with audit.

## 2. Persona and ruleset

| Item | Value |
|---|---|
| Simulated business | Electrical / home-service SMB (appointment-driven) |
| Ruleset ID | `demo-electrical-smb-v1` |
| Qualify if | In service area AND job type in {service call, install, panel} AND not spam |
| Not qualify if | Outside area, unsupported job, clearly non-buyer |
| Needs human if | Ambiguous address, insurance/commercial complexity, low match confidence, angry tone |

## 3. Happy path (sequence)

```text
[D0] Operator injects synthetic inquiry (fixture contact namespace)
        ↓
[D1] Capture — create/match contact (search → enrich → update if conf≥0.85)
        ↓
[D2] Opportunity create (R2 approval) → stage Qualifying
        ↓
[D3] Qualify (rules) → stage Qualified | Disqualified | Exception
        ↓
[D4] Note + task (appointment-setting) + message.draft (R2 approval)
        ↓
[D5] Human books appointment in GHL calendar (human.appointment.book)
        ↓
[D6] Verify via crm.appointment.read → terminal Outcome + cost
        ↓
[D7] Scorecard row written for demo cohort
```

### Exception path

Any step may branch to `needs_human` / failed side-effect:

```text
uncertain or failed
  → crm.note.create (exception context)
  → crm.task.create (owner = exception owner)
  → Outcome status = exception (open|closed)
  → no silent retry on ambiguous writes
```

## 4. Step contract

| Step | Actor | Capabilities | Gate | Evidence |
|---|---|---|---|---|
| D0 Inject | Operator | — | Manual | Fixture ID, `cohort=demo_rcaos_v1` |
| D1 Capture | System | `crm.contact.search/read/enrich/update` | R2 on update | Contact ID, matchConfidence |
| D2 Opportunity | System | `crm.opportunity.create` | R2 approval | Opportunity ID, stage |
| D3 Qualify | System + rules | `crm.opportunity.update`, `crm.note.create` | R2 on stage | Qualification label |
| D4 Prepare | System | `crm.task.create`, `crm.message.draft` | R2 on draft | Task ID, draft payload |
| D5 Book | Human | `human.appointment.book` | Human | Calendar UI confirmation |
| D6 Verify | System | `crm.appointment.read` | — | Appointment ID or exception |
| D7 Report | System / FDE | Outcome + cost | — | Scorecard row |

## 5. Fixture isolation (mandatory)

| Control | Rule |
|---|---|
| Namespace | Demo contacts use dedicated email domain / tag e.g. `+rcaos-demo@` or GHL tag `aion-rcaos-demo` |
| Pipeline | Prefer acceptance/demo pipeline stages; restore or leave clearly marked |
| Production economic flag | `productionEconomic=false` |
| Real prospects | Forbidden as demo subjects |
| Send | Forbidden even if keys exist |

## 6. Acceptance mapping

| AT | Demo proof |
|---|---|
| AT-1 | D1 contact exists with execution audit |
| AT-2 | Re-inject same phone/email → no second contact |
| AT-3 | Rules force each of three labels on three fixtures |
| AT-4 | Draft requires approval; send capability unused |
| AT-5 | After human book, read returns appointment **or** logged exception |
| AT-6 | Scorecard row with six metrics (demo n may be small; label limitations) |
| AT-7 | Inject denied upsert (low confidence) → no partial write |

## 7. Out of demo scope

- Public webform / missed-call webhook ingest  
- Live model classification dependency as blocker for D3 (rules engine may be deterministic for demo; model assist optional)  
- Multi-location  
- Revenue Copilot call intelligence (separate Gate A / AIO-70)  

## 8. Handoff to Sprint 2

Sprint 2 implements D0–D7 on Runtime with:

1. Authorized actions wired through Execution Gateway  
2. Approval + exception gates exercised on real Empire tenant  
3. End-to-end Outcome path  
4. Pilot demonstration package (script + evidence IDs + scorecard)  

Do not mark Sprint 1 as “pilot complete.”
