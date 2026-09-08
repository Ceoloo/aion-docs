# AION Operating Leverage

Post-`execution-platform-v0.2.0` chapter. The platform-construction era is closed.
Engineering milestones no longer lead the roadmap — **business operations do**.

**Immutable baseline:** [`execution-platform-v0.2.0`](../releases/execution-platform-v0.2.0.manifest.json)
(M001–M009 certified). Do not move that tag. Do not add foundational architecture
unless real operations expose a missing primitive.

**Empire thesis:** the business compounding loop (attention → revenue → capability →
content → attention) is [`aion-flywheel.md`](aion-flywheel.md). Operating leverage
is how we **measure and run** the first turn of that flywheel — not a substitute
for it.

---

## North star

Convert AION Execution Platform from proven infrastructure into a **machine
workforce** that produces measurable revenue and client outcomes with
**progressively less human labor per dollar produced**.

Scoreboard (above the engineering roadmap):

| Metric | Why it matters |
|---|---|
| Revenue influenced | Top-of-funnel machine impact |
| Revenue actually collected | Cash, not story |
| Pipeline created | Forward value |
| Economic value / execution | Unit productivity |
| Economic value / $1 execution spend | Machine leverage |
| Missions completed / week | Throughput |
| Success rate | Reliability |
| Human minutes / mission | Remaining labor |
| Cost / successful mission | Unit cost |
| Client gross margin | Service economics |

**Watch especially:** human minutes required per $1,000 of economic value produced.
If AION grows while that number falls, the leverage thesis is working.

---

## Strategic rule

Until v0.2.0, engineering milestones drove the roadmap.

From v0.2.0 forward:

1. **Run the company** on the certified platform.
2. When operations expose a gap (“we can’t process enough missions because X”),
   **build X**.
3. Do **not** build M010 (or any new architecture milestone) because M009 exists.
4. Prefer operator UI and production loops over new backend primitives.

Autonomy (L0–L4) is infrastructure, not the goal. The goal is:

> Remove humans where humans aren’t economically useful while retaining them
> where judgment improves outcomes.

Production evidence drives autonomy changes — not a push to get everything to L4.

---

## Program: OL-001 → OL-005

### OL-001 — Revenue Production Loop

A **real operating workload**, not an architecture mission. **Dominant priority**
after v0.2.0 — but **paused** until **live GHL access** and **live model access**
are both real **and verified**.

**Status (2026-09-08):** live GHL access **verified** on production Runtime
(`ghl-live`, capability matrix in
[ghl-readonly-governed-write.md](./ghl-readonly-governed-write.md)) after PIT
rotation. Live model access **not verified** — OpenRouter / Revenue Copilot not
available to the proof harness yet. **Do not unpause OL-001** until the model
gate passes, then re-run `npm run proof:ghl-live-capability` end-to-end.

**Measurement honesty:** PRE-OL validation missions (Command Center system
tests, Console launches while paused) are recorded separately — see
[pre-ol-validation.md](./pre-ol-validation.md). They must show as
`cohort=pre_ol_validation` / `productionEconomic=false` and **never** inflate
`OL-001 progress = N / 100`. GHL reads + governed write + replay are green;
**next gate is live model**, then OL-001 Mission 001 / 100.

```text
REAL LEAD
  → GHL
  → AION Mission
  → Research / enrichment
  → Qualification
  → Human / AI sales interaction
  → Revenue Copilot
  → Follow-up
  → Opportunity progression
  → Proposal / application
  → Outcome
  → REVENUE
  → Evaluation + economics
```

**First production target:** **100 real revenue missions** through v0.2.0.

Run repeatedly — not once. Version meaningful workflow changes so cohort data
stays comparable (`Revenue Production v1`, `Lead-to-Appointment v1` /
[SA-STD-001](./secure-automation-deployment-standard.md), …). Do **not** continuously
redesign the workflow across the entire 100 without versioning.

#### Cohort checkpoints (production experiment)

| Missions | Mode |
|---|---|
| **1–10** | Supervised production — inspect essentially every execution and intervention |
| **11–25** | Stabilize recurring failures; remove obvious operator friction |
| **26–50** | Compare workflow versions, routing recommendations, intervention patterns |
| **51–75** | Cautiously exercise earned autonomy where M008 evidence supports it |
| **76–100** | Closer to steady state; establish first trustworthy production baseline |

#### Primary KPIs

| KPI | Question |
|---|---|
| Mission throughput | How much useful work AION completes |
| Success rate | Whether workflows reliably finish |
| Human intervention rate | How much human labor remains |
| Cost / mission | Machine cost of producing work |
| Economic value / execution | Whether machine labor creates value |
| Economic value / cost | Overall machine-workforce leverage |

Secondary: approval wait time, time-to-outcome, retry rate, provider/model
performance, workflow step failure rate, tenant-level economics.

#### Revenue identity (protect aggressively)

Keep these **separate** on every scoreboard and rollup:

| Field | Meaning |
|---|---|
| **Pipeline created** | Forward opportunity value — not cash |
| **Revenue influenced** | Soft attribution / assist — not collected |
| **Revenue attributed** | Execution-level attributed EV (M005) |
| **Revenue collected** | Actual cash received |

`pipeline ≠ attributed ≠ collected`. Collapsing them produces impressive but
economically meaningless ROI.

#### OL-001 scoreboard (heartbeat)

```text
OL-001 — REVENUE PRODUCTION
PRODUCTION
  Real missions             N / 100
  Successful                     —
  Failed                         —
  In progress                    —
THROUGHPUT
  Missions / day                 —
  Median completion time         —
HUMAN LOAD
  Intervention rate              —
  Approvals / mission            —
  Human minutes / mission        —
ECONOMICS
  Cost / mission                 —
  Cost / successful mission      —
  Economic value / execution     —
  EV / execution cost            —
REVENUE
  Pipeline created               —   (≠ attributed ≠ collected)
  Revenue influenced             —
  Revenue attributed             —
  Revenue collected              —
RELIABILITY
  Failure rate                   —
  Retry rate                     —
  Policy violations              —
  External-system failures       —
```

**Major checkpoint:** scoreboard reads **100 / 100** with enough evidence to
answer how productive, reliable, expensive, autonomous, and economically
valuable the machine workforce is in the real world.

UI/backend changes during OL-001 are **pulled by friction**, not pre-built.
Next Console slices when needed: create-mission (first), then policy-aware retry.

---

### OL-002 — Client Production Loop

**Deferred until OL-001 baseline exists.** Do not onboard client tenants to prove
scalability before proving the internal revenue loop produces measurable value.

```text
                 AION PLATFORM v0.2
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
     AION Revenue    Client A      Client B
          │             │             │
          └─────────────┼─────────────┘
                        ↓
              Shared capabilities
                        ↓
                isolated execution
```

Start with one client. Do not onboard ten businesses to prove scalability before
proving one produces measurable value.

---

### OL-003 — Unit economics

M005 economics become commercially decisive.

```text
AI execution cost
+ external tool cost
+ allocated infrastructure cost
+ human intervention cost
────────────────────────────
TOTAL DELIVERY COST
versus
Revenue generated
+ attributable pipeline value
+ labor saved
+ client value created
────────────────────────────
ECONOMIC VALUE
```

Target clarity: know actual service margins (machine + external + human vs fee).

---

### OL-004 — Workforce optimization

After enough production executions, M007 scorecards have meaningful evidence.

Optimize: model routing → workflow version → agent selection → autonomy → cost envelope.

Examples production may prove:

- Expensive model for qualification; cheap model for enrichment
- One workflow version converts materially better
- Human approval improves proposals but adds nothing to CRM enrichment

Let the execution dataset compound.

---

### OL-005 — Productization

Only after OL-001–004 loops work repeatedly.

Internal stack remains **AION Execution Platform**.
Customer-facing product becomes **AION Workforce**:

```text
AION WORKFORCE
  → Client Workspace / GHL
  → Company-specific knowledge
  → Revenue / Marketing / Ops Agents
  → AION Execution Platform
  → Governed services
  → Models + tools
  → Measured business outcomes
```

Grok Bot installation can evolve from low-ticket entry into managed AI-workforce.

**M010** (legacy roadmap row) is absorbed here — monetize only after operating
loops prove value. Do not start M010 as an architecture spike.

---

## Interaction model

| Surface | Role |
|---|---|
| **Operator UI** | Day-to-day operation of AION |
| **Code (Cursor / Claude Code)** | Developing AION — schema, adapters, policy, CI |
| **GHL / SaaS** | Domain-specific human CRM workflows |
| **AION Runtime** | Actual authority |

UI never becomes the business-logic layer. Every button invokes an existing
governed capability.

```text
                 HUMAN OPERATORS
                      │
     ┌────────────────┼────────────────┐
     ▼                ▼                ▼
Command Center   Mission Control   Client Ops
     │                │                │
     └────────────────┼────────────────┘
                      ▼
               AION API / BFF
                      │
           ┌──────────┴──────────┐
           ▼                     ▼
     Execution Platform       AION Data
           │
     Runtime / Gateway
           │
    External systems/tools
```

Planned hubs (thin shell, role-specific views — not one giant dashboard):

1. **Command Center** — Holding view: missions, spend, EV, failures, approvals, workforce
2. **Mission Control** — create/inspect missions, lineage, pause/resume, retry, approve, costs
3. **Workforce Hub** — agents, roles, autonomy grants, scorecards, routing (later)
4. **Service Catalog UI** — capabilities, versions, risk, consumers (later)
5. **Client Ops UI** — GHL-facing workspace; AION remains governance truth (later)

Keep configuration-heavy engineering in Git + CI: migrations, service registration
internals, risk policy code, provider adapters, deploy config.

---

## UX-001 — AION Operator Console

**Not another backend mission.** Human operating surface over certified v0.2.0.

**Prerequisite:** [OPS-001](ops-001-live-runtime.md) — live Runtime HTTPS behind
Traefik. Do not deploy the Console against a stub Runtime.

**Home:** `aion-products/workforce-control` (evolves M006 Control Center).
Reuse Vite + shadcn already wired to canonical Runtime APIs.

### Interaction model (steady state)

```text
Open AION
  → COMMAND CENTER — "What needs my attention?"
  → Act (approve / launch / inspect)
  → AION executes via Runtime
```

Cursor / Claude Code remain the engineering cockpit. The Console runs the machine.
Natural language later is another interface into the **same governed services** —
never a privileged backdoor.

### Landed flows

1. Command Center OL scoreboard + Mission Control filters
2. Inspect execution lineage / agent / tenant-company scope
3. **Approve / deny** via `POST /v1/approvals/:id/decision`
4. View mission economics
5. **Create / launch mission** via `POST /v1/missions/run` (canonical contract — no UI-specific execution path)

### Pulled by friction (do not pre-build)

- **Policy-aware retry** when genuine retry friction appears:

  ```text
  Execution failed · GHL timeout
  Retry eligibility: ALLOWED | REFUSED (side-effect / non-idempotent)
  Attempts: 1 / 3
  [ Retry execution ]  — only when ALLOWED
  ```

  Refuse simple retry when side effects may not be idempotent; escalate instead.

- Console v2 features discovered from OL-001 behavior (“I keep opening this”,
  “I never use that metric”, “too many clicks”)

### Create-mission contract

Launch submits the same Runtime body proofs use:

- inline `mission` + versioned `workflow` (e.g. `Revenue Production v1`)
- governed `actor` with tenant scope + permissions
- optional `stepPayloads` / `metadata` (budget, cohort tags)

Autonomy remains **policy managed** — the form does not raise L-levels.

### Non-goals

- New Core contracts or migrations for Console convenience
- Auto-adaptive routing or autonomy promotion from the UI
- Starting OL-002 / Client Ops before OL-001 completes
- Replacing GHL as CRM truth

### Done when (UX-001 slice)

Operators initiate normal production work and handle approvals from the Console
without living in the IDE — while all writes still go through Runtime governance.
