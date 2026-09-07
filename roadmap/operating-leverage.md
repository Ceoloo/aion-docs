# AION Operating Leverage

Post-`execution-platform-v0.2.0` chapter. The platform-construction era is closed.
Engineering milestones no longer lead the roadmap — **business operations do**.

**Immutable baseline:** [`execution-platform-v0.2.0`](../releases/execution-platform-v0.2.0.manifest.json)
(M001–M009 certified). Do not move that tag. Do not add foundational architecture
unless real operations expose a missing primitive.

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

A **real operating workload**, not an architecture mission.

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

Enough volume for operational patterns; small enough to inspect failures manually.
Run repeatedly — not once.

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

---

### OL-002 — Client Production Loop

Once the internal revenue loop is stable, run **one** real client tenant on the
same platform.

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

## UX-001 — AION Operator Console (next milestone)

**Not another backend mission.** First production operator surface.

**Home:** `aion-products/workforce-control` (evolves M006 Control Center).
Reuse Vite + shadcn stack already wired to canonical Runtime APIs — do not
rebuild in `aion-desks` (marketing/checkout) or invent a parallel Next app unless
operator behavior later demands a split.

### First production flows

1. See active / completed / failed missions
2. Inspect execution lineage
3. **Approve / deny** pending actions (governed Runtime decision)
4. View mission economics
5. Inspect failures
6. See tenant / company scope
7. Inspect agent / service responsible for an execution
8. (Next slice) Create a mission from a business objective via `POST /v1/missions/run`

### Non-goals for UX-001

- New Core contracts or migrations
- Auto-adaptive routing or autonomy promotion from the UI
- Broad Client Ops / multi-tenant admin suite
- Replacing GHL as CRM truth

### Done when

Operators can run daily revenue/client loops from the console without living in
the IDE for approvals, mission inspection, lineage, and economics — while all
writes still go through Runtime governance.
