# Baseline method — outcome scorecard

**Status:** Locked for Sprint 1 (2026-10-12)  
**Rule:** No percentage improvement promises until Baseline is filled from
client (or demo) evidence.

---

## 1. Purpose

Establish a dated, attributable starting point for the six outcome metrics so
pilot reports compare **observed vs baseline**, not vs aspiration.

## 2. Measurement window

| Window | Default | Notes |
|---|---|---|
| **Baseline lookback** | Prior **30 days** (prefer 90 if volume is low) | Ends the day before pilot start |
| **Pilot window** | **14–30 days** after acceptance tests green | Declared in the process contract |
| **Timezone** | Client business timezone | Record explicitly |

## 3. Metric definitions (normative)

| Metric | Definition | Numerator | Denominator | Primary evidence |
|---|---|---|---|---|
| **First meaningful response time** | Time from inquiry received → first human or approved system response that advances the lead (not auto-ack alone) | Sum of response latencies (or median reported) | Inquiries with known receive timestamp | Channel timestamp + CRM activity / note |
| **Lead-to-qualified rate** | Share of inquiries reaching `Qualified` under tenant rules | Count qualified | Count inquiries in window | Opportunity stage / qualification Outcome |
| **Qualified-to-booked rate** | Share of qualified leads with **verified** appointment | Count booked (verified) | Count qualified | `crm.appointment.read` evidence or signed booking record |
| **Workflow completion rate** | Share of inquiries that reach a terminal Outcome (`booked`, `disqualified`, `exception_closed`) | Terminal Outcomes | Inquiries started | AION Outcome records |
| **Exception and error rate** | Share requiring human exception path or failed governed action | Exceptions + failed executions | Inquiries started | Exception Outcomes + approval denials + failed side-effects |
| **Verified value − operating cost** | Attributed revenue or EV from booked/won work minus execution + labor cost in window | Verified value | — (report net and both sides) | CRM won/estimate fields + Runtime cost units + human minutes |

### Attribution rules

1. Prefer **median** for response time when n ≥ 10; otherwise report median and n.  
2. An inquiry counts once; duplicates merged by identity rules do not inflate denominators.  
3. “Booked” requires verify step — calendar hold without CRM evidence is not booked.  
4. Value may be **estimated** (job EV) or **realized** (won revenue); label which.  
5. If attribution is weak, report the metric as **unknown** — do not invent ROI.

## 4. Data collection procedure

### Step B1 — Inventory sources

| Source | Pull |
|---|---|
| GHL contacts / opportunities / appointments | Counts, stage history, created/updated timestamps |
| Conversations / call logs / forms | Inquiry receive timestamps (if available) |
| Operator interview | After-hours handling, known drop-offs, manual sheets |
| AION Runtime (if already used) | Prior execution/Outcome samples |

### Step B2 — Compute baseline table

Fill for the lookback window:

```text
inquiries_n =
qualified_n =
booked_n =
terminal_n =
exception_n =
median_first_response =
verified_value =
operating_cost_estimate =
```

### Step B3 — Record limitations

Document missing timestamps, channels not in CRM, and any manual reconstruction.
Limitations travel with every client-facing scorecard.

### Step B4 — Set pilot targets (after baseline only)

Targets are **directional** (e.g. “reduce median first response”) unless the
client explicitly accepts a numeric goal. Never backfill targets before baseline.

## 5. Demo-client baseline (AION Empire)

| Field | Locked approach |
|---|---|
| Client | AION Empire customer-zero |
| Lookback | Last 30 days of Empire GHL opportunity/appointment activity where timestamps exist |
| If volume too low | Mark baseline metrics `insufficient_n` and use synthetic pilot cohort for process reliability metrics only (completion / exception / cost) — **do not** claim conversion lift |
| Value field | Use `$0` or explicit EV placeholder for synthetic bookings; never mix synthetic into commercial ROI claims |
| Reporting label | `cohort=demo_rcaos_v1` / `productionEconomic=false` for synthetic |

## 6. Scorecard output (minimum)

Every baseline and pilot report includes:

1. Window start/end + timezone  
2. Table of six metrics (baseline / target / observed)  
3. n for each rate  
4. Attribution limitations  
5. Cost units + human minutes (pilot)  
6. Explicit statement of what was **not** measured  

## 7. Anti-patterns

- Averaging response time without stating n  
- Counting drafts or tool successes as bookings  
- Using token spend as the primary KPI  
- Comparing pilot to an undated “industry average” instead of client baseline  
