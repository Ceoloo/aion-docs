# Required GHL capabilities — Revenue Capture & Appointment OS v1

**Status:** Locked for Sprint 1 (2026-10-12)  
**Sources:** Mission 009 / AIO-17 catalog · [ghl-readonly-governed-write.md](../../roadmap/ghl-readonly-governed-write.md) · Runtime `docs/ghl-aio17-lead-workflow.md`

Capabilities are catalog `serviceKey`s. The business capability
`qualify_lead_and_prepare_booking` **composes** these keys; it is not itself a
catalog entry until platformized.

---

## 1. Required for Sprint 1 design / Sprint 2 pilot path

| serviceKey | Role in process | Risk / gate | Catalog status (as of AIO-17) |
|---|---|---|---|
| `crm.contact.read@1` | Load contact for dedupe / verify | R1 | Active |
| `crm.contact.search@1` | Find duplicates by phone/email | R1 | Active |
| `crm.contact.enrich@1` | Normalize attributes for qualification | R1 | Active |
| `crm.contact.update@1` | Upsert matched contact | R2 + approval; conf ≥ 0.85 | Active |
| `crm.opportunity.read@1` / `search@1` | Pipeline state | R1 | Active |
| `crm.opportunity.create@1` | Open opportunity for inquiry | R2 + approval | Active |
| `crm.opportunity.update@1` | Stage moves (qualify / booking) | R2 + approval | Active |
| `crm.pipeline.read@1` | Stage map for tenant | R1 | Active |
| `crm.note.create@1` | Qualification / exception notes | R1 gateway | Active |
| `crm.task.create@1` | Appointment-setting handoff | R1 gateway | Active |
| `crm.message.draft@1` | Approved follow-up copy (no send) | R2 + approval | Active (draft) |
| `crm.appointment.read@1` | **Verify** booking evidence | R1 | Active (read) |

## 2. Explicitly deferred (v1)

| serviceKey / action | Why deferred | Sprint impact |
|---|---|---|
| `crm.message.send@1` | R3 customer impact; policy not ready for flagship | Human sends from draft |
| `crm.conversation.read@1` | AIO-17 `CAPABILITY_DISABLED` | Demo uses synthetic inquiry, not live thread ingest |
| `crm.conversation.send` | Same | Out of scope |
| `crm.appointment.create@1` | Inactive / reserved until certified | Human books; verify via read |
| `crm.appointment.update@1` | Inactive | Out of scope |
| Calendar availability / conflict APIs | Not in enabled slice | Human scheduling judgment |
| Messaging webhooks / delivery receipts | Not in enabled slice | Scorecard uses CRM + Outcome timestamps |

## 3. Composition — business capability → serviceKeys

```text
qualify_lead_and_prepare_booking
  ├─ crm.contact.search / read / enrich [/ update]
  ├─ crm.opportunity.create / update
  ├─ crm.note.create
  ├─ crm.task.create
  ├─ crm.message.draft          (approval-gated)
  ├─ human.appointment.book     (human-operated)
  └─ crm.appointment.read      (verify → Outcome)
```

Aligned with Secure Automation `lead-to-appointment-v1` steps in `@aion/core`.

## 4. Sprint 2 enablement checklist (not claimed done)

- [ ] Tenant pipeline/stage IDs configured (`GHL_ACCEPTANCE_*` or client equivalents)  
- [ ] Demo contact namespace / tag isolation so synthetic runs never touch real prospects  
- [ ] Approval principals registered for R2 opportunity + draft steps  
- [ ] Appointment verify path: read returns booking **or** exception Outcome  
- [ ] Cost units recorded on terminal Outcome  
- [ ] No enablement of `crm.message.send` or `crm.appointment.create` without new acceptance evidence  

## 5. Honesty note

Live GHL **reads** and selected **governed writes** (note/task/opportunity) are
already proven on AION Empire production Runtime for earlier gates. That does
**not** mean Appointment OS v1 is production-ready for external clients.
Conversation ingest, send, and appointment writes remain open work.
