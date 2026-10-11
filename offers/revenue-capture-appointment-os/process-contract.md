# Process contract — Revenue Capture & Appointment OS

**Contract ID:** `RCAOS-PC-v1`  
**Status:** Locked for Sprint 1 (2026-10-12)  
**Workflow alignment:** Secure Automation `lead-to-appointment-v1` (`@aion/core`)

Use Part A as the reusable template. Part B is the locked demo-client instance.

---

## Part A — Template (copy per client)

### A1. Parties and window

| Field | Value |
|---|---|
| Client | |
| Tenant / location | |
| Systems of record | GHL (CRM + calendar evidence) · AION Runtime (governance) · Supabase (canonical outcomes when wired) |
| Measurement window | ____ days starting ____ (recommend 30–90 day lookback for baseline; 14–30 day pilot window) |
| Exception owner (business hours) | |
| Exception owner (after hours) | |
| AION FDE owner | |

### A2. Outcome promise (plain language)

Reduce missed inquiries, improve qualification and booking follow-through, and
make every lead’s status measurable — without autonomous customer messaging or
ungoverned CRM writes.

### A3. In-scope process

1. Capture inbound inquiry (or synthetic demo inquiry)  
2. Identify duplicate / existing contact  
3. Qualify against tenant rules → `qualified` / `not_qualified` / `needs_human`  
4. Prepare approved follow-up **draft** (no send in v1)  
5. Create appointment-setting task + CRM notes  
6. Escalate uncertain cases to a human  
7. Human books appointment; system **verifies** appointment evidence via read  
8. Report scorecard metrics + operating cost  

### A4. Business capability (unit of execution)

| Field | Value |
|---|---|
| Capability ID | `qualify_lead_and_prepare_booking` |
| Type | Product / Secure Automation workflow composing catalog `serviceKey`s |
| Promotion | Not a first-class platform `serviceKey` until reuse proves platformization |
| Underlying workflow | `lead-to-appointment-v1` |

### A5. Autonomy / approval matrix

| Step | Action tier | Risk | Approval | Notes |
|---|---|---|---|---|
| Contact read / search / enrich | Observe | R1 | No | Dedupe signals |
| Contact update (upsert) | Execute | R2 | Yes | `matchConfidence ≥ 0.85` |
| Opportunity create / stage update | Execute | R2 | Yes | Pipeline movement |
| Note / task create | Execute | R1 | No (gateway-governed) | Handoff evidence |
| Message draft | Assist | R2 | Yes | Draft only |
| Message send | — | R3 | — | **Out of scope v1** |
| Appointment create (API) | — | — | — | **Inactive** — human books |
| Appointment read / verify | Observe | R1 | No | Success evidence |

### A6. Baseline + success measures

See [baseline-method.md](baseline-method.md). Minimum scorecard:

| Metric | Baseline | Pilot target | Observed |
|---|---|---|---|
| First meaningful response time | | (set after baseline) | |
| Lead-to-qualified rate | | | |
| Qualified-to-booked rate | | | |
| Workflow completion rate | | | |
| Exception and error rate | | | |
| Verified value − operating cost | | | |

No percentage improvement promises without filled Baseline column.

### A7. Explicit non-goals (v1)

- Autonomous SMS/email send  
- Ungoverned calendar API booking  
- Multi-location / multi-CRM orchestration  
- Replacing the client’s CRM  
- Guaranteed revenue lift percentages  
- Counting tool calls as success  

### A8. Acceptance tests

| ID | Test | Pass criteria |
|---|---|---|
| AT-1 | Inbound capture | Inquiry creates or matches a contact with audit trail |
| AT-2 | Dedupe | Known contact is not duplicated under identity rules |
| AT-3 | Qualification | Rules emit `qualified` / `not_qualified` / `needs_human` |
| AT-4 | Follow-up gate | Outbound **draft** requires approval; send remains disabled |
| AT-5 | Booking verify | Appointment evidence via `crm.appointment.read` **or** explicit exception outcome with owner |
| AT-6 | Scorecard | Report includes the six outcome metrics for the window |
| AT-7 | Fail-closed | Denied / failed actions leave no silent partial writes; ambiguous writes not auto-replayed |

---

## Part B — Demo client instance (locked)

| Field | Value |
|---|---|
| Client | **AION Empire — customer-zero / internal demo** |
| Tenant | `aion-systems` (GHL location bound via Runtime env) |
| Purpose | Prove the governed process end-to-end before external SMB pilot |
| Channels in scope (demo) | Manual / synthetic inquiry injection (not live public forms yet) |
| Qualification ruleset | `demo-electrical-smb-v1` — service area, job type, urgency, budget signal → qualify / not / needs_human |
| Measurement window | Baseline: last 30 days of Empire pipeline where available; Pilot design: 14-day synthetic cohort |
| Exception owner | CEO LOO / on-duty operator |
| AION FDE owner | Sprint FDE (assigned at Sprint 2 kickoff) |
| Outcome promise | Every demo inquiry reaches a terminal Outcome: booked (human-verified) **or** exception with owner — with cost units |
| Prohibited | Live send to real prospects; mutating non-demo contacts without approval |

### Demo identity rules (dedupe)

Match order: phone (E.164) → email (normalized) → name+zip soft match.  
Upsert only when `matchConfidence ≥ 0.85`; else `needs_human`.

### Demo pipeline stages (logical)

`New Inquiry` → `Qualifying` → `Qualified` → `Booking` → `Booked` / `Disqualified` / `Exception`

Map to existing GHL pipeline IDs at Sprint 2 config time (`GHL_ACCEPTANCE_*` pattern).

### Sign-off

| Role | Name | Date | Decision |
|---|---|---|---|
| Decision owner | CEO LOO | 2026-10-12 | Process contract v1 locked for Sprint 1 |
| Architecture | aion-docs | 2026-10-12 | Aligns with ADR-014 Accepted |
