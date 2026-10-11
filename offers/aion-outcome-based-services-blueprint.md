# AION Outcome-Based Services Blueprint

**Status:** Operating model accepted via ADR-014 (2026-10-12); Sprint 1 definition locked  
**Decision owners:** CEO LOO  
**Related ADR:** [ADR-014](../adr/ADR-014-outcome-based-systems-engineering.md) (**Accepted**)  
**Flagship pack:** [revenue-capture-appointment-os/](revenue-capture-appointment-os/)  
**Mission:** [MISSION-RCAOS-001](../missions/MISSION-RCAOS-001-revenue-capture-appointment-os.md)  
**Evidence state:** Commercial/architecture definition locked — not verified production or external pilot milestones

This blueprint connects commercial offers, product architecture, Forward Deployment
Engineer (FDE) training, sales process, and the performance scorecard into one
operating model under **Outcome-Based Systems Engineering**.

---

## 1. Strategic thesis

As AI gains easier access to business software (vendor MCP servers, headless
business APIs, native agent experiences), competitive advantage shifts from
**building integrations** to **owning measurable business outcomes**.

AION does not compete on how many tools agents can access. AION competes on how
reliably systems complete **revenue-generating and operations-critical work**,
under governance, with verified results.

**Strategic risk:** Software vendors will not stop at MCP access. They will ship
their own orchestration, agent UIs, and native automations. Orchestration alone
is not a permanent moat.

**Defensibility:** Industry-specific expertise, reusable governed workflows,
proven implementation quality, client relationships, and **verified operational
results**.

---

## 2. Positioning

### Old positioning (retire for new work)

> We build AI agents, CRM automations, and custom software integrations for businesses.

### New positioning (adopt for messaging and packaging)

> We design, deploy, and operate intelligent business systems that turn existing software into measurable results.

### Relationship to prior decisions

| Prior record | How this blueprint relates |
|---|---|
| Sep 2025 consulting positioning (Notion Offer Catalog) | **Evolves, does not erase.** Diagnosis, strategy, and advisory remain the commercial front door. Delivery hardens into outcome-based systems with acceptance tests and scorecards. |
| Agentic Systems as a Service (Notion) | **Aligns.** Still “operate digital workforces across existing software.” Adds: business capabilities as the unit of execution; flagship appointment OS; explicit outcome scorecard. |
| TechOps ladder ([offers/README.md](README.md)) | **Complementary.** TechOps is the secure foundation that makes autonomous digital labor operable. Outcome systems sit on that foundation; they do not replace it. |
| Operating Leverage ([roadmap/operating-leverage.md](../roadmap/operating-leverage.md)) | **Reinforces.** Business operations lead; engineering builds only when ops expose a gap. |

Clients purchase the **result**. AION is accountable for the **process** that produces it.

---

## 3. Three commercial pillars

| Offering | What the client actually buys | Primary AION surface |
|---|---|---|
| **Revenue Systems** | More qualified opportunities, faster follow-ups, better conversion | Revenue Copilot + governed revenue workflows |
| **Operations Systems** | Less manual coordination, fewer errors, faster fulfillment | Governed ops capabilities (scheduling, dispatch, status) |
| **Managed AI Infrastructure** | Reliability, governance, monitoring, optimization | Agent OS / Execution Gateway + TechOps Continuous Assurance |

Land with one repeatable Revenue System first. Expand into Operations and deepen
Managed AI Infrastructure only after the flagship process is proven under
measurement.

---

## 4. Target architecture (capability-centric)

Do **not** start by building another platform. Map outcomes onto architecture
already underway.

```text
                    ┌─────────────────────────────────────┐
                    │     Outcome Verification + Reporting │
                    │  completion · exceptions · cost · ROI │
                    └─────────────────┬───────────────────┘
                                      │
                    ┌─────────────────▼───────────────────┐
                    │         AION Revenue Copilot          │
                    │   business process + outcome tracking │
                    └─────────────────┬───────────────────┘
                                      │
                    ┌─────────────────▼───────────────────┐
                    │            AION Agent OS              │
                    │ orchestration · permissions · gates   │
                    │         evals · audit logs            │
                    └─────────────────┬───────────────────┘
                                      │
                    ┌─────────────────▼───────────────────┐
                    │   Execution Gateway + MCP/API Adapters│
                    │     controlled approved actions       │
                    └─────────────────┬───────────────────┘
                                      │
              ┌───────────────┬───────┴────────┬──────────────┐
              ▼               ▼                ▼              ▼
         GoHighLevel      Supabase      Other client     Human gates
                                        systems
```

**Proposed target architecture** — not a claim that every connection is
production-ready. Production rollout remains gated on testing, permissions, and
operational acceptance. See live GHL / model gates in
[roadmap/ghl-readonly-governed-write.md](../roadmap/ghl-readonly-governed-write.md).

### Repo mapping (canonical)

| Blueprint layer | Canonical home |
|---|---|
| Agent OS (orchestration, permissions, approvals, evals, audit) | `aion-core` + Runtime composition |
| Execution Gateway + adapters | `aion-runtime` ([ADR-003](../adr/ADR-003-execution-gateway-into-runtime.md)) |
| Outcomes, events, economic records | `aion-data` |
| Revenue Copilot / workforce consoles | `aion-products` |
| Environments, secrets, deploy | `aion-infra` |
| Constitution / this blueprint | `aion-docs` |

### Architectural priority: business capabilities

Make **business capabilities** the unit of execution, not raw tool calls.

| Anti-pattern | Target pattern |
|---|---|
| Agent freely chains `ghl.search_contact`, `ghl.create_note`, `calendar.slot` | Governed operation `qualify_lead_and_prepare_booking` |
| Success = tool returned 200 | Success = verified CRM + calendar state + outcome record |
| Auth = vendor API key in agent prompt context | Auth = Execution Gateway policy, identity, approval, audit |

A business capability:

1. Declares inputs, preconditions, and business rules  
2. Invokes one or more underlying tools/adapters  
3. Applies validation and exception handling  
4. Escalates uncertain cases to a human when policy requires  
5. Emits an **Outcome** (distinct from a tool Result) with cost and evidence  

MCP / vendor APIs improve scalability and authorization plumbing. **Governance
logic remains AION’s responsibility** (permissions, risk tiers, human gates,
evals, audit). See [governance/action-tiers.md](../governance/action-tiers.md)
and [architecture/execution-layer.md](../architecture/execution-layer.md).

### Example capability contract (illustrative)

```text
capability: qualify_lead_and_prepare_booking
inputs:    inquiry_id, channel, tenant_ruleset_id
preconditions: contact identity resolvable OR create-allowed
steps:     dedupe → qualify → draft follow-up → propose booking → escalate if uncertain
side_effects: only via approved serviceKeys (GHL / calendar / messaging)
success:   appointment verified OR explicit exception outcome with owner
audit:     executionId, outcomeId, cost, policy decision, approver if gated
```

---

## 5. Flagship offer — Revenue Capture & Appointment OS

### Promise

Reduce missed inquiries, improve qualification and booking follow-through, and
make every lead’s status measurable.

### Target clients

Contractors, electrical service businesses, home-service companies, and other
**appointment-driven SMBs**.

### Complete process (build once; configure per customer)

1. Capture inbound inquiries from connected channels  
2. Identify duplicate or existing contacts  
3. Qualify against business-specific rules  
4. Prepare or send approved follow-ups  
5. Coordinate calendar bookings  
6. Escalate uncertain cases to a human  
7. Verify appointment creation and CRM updates  
8. Report response time, qualified bookings, exceptions, and operating cost  

### Commercial packaging (working model)

| Stage | Client buys | Notes |
|---|---|---|
| Business Audit / baseline | Diagnosis + scorecard baseline | Front door; may be free fit call or paid diagnostic — declare which before booking |
| Design & deploy | Configured Revenue Capture & Appointment OS | One-time / milestone project scoped to approved process |
| Operate & optimize | Managed AI Infrastructure + outcome reviews | Recurring; includes monitoring, rule updates, exception handling |
| Expand | Additional Revenue / Operations capabilities | Land-and-expand after verified value |

Exact fees remain founder-owned; do not invent a rate card here.

### Required GHL capability set

**Locked:** [revenue-capture-appointment-os/ghl-capabilities.md](revenue-capture-appointment-os/ghl-capabilities.md).

v1 uses governed contact/opportunity/note/task/`message.draft`/`appointment.read`.
**Deferred:** `crm.message.send`, `crm.appointment.create`, live conversation
ingest. Human books; system verifies via read.

Prefer extending the existing governed GHL path over new substrate. See
[roadmap/ghl-readonly-governed-write.md](../roadmap/ghl-readonly-governed-write.md).

---

## 6. Performance scorecard — measure outcomes, not agent activity

Set the baseline with client data **before** promising percentage improvements.
Distinguish targets, estimates, and observed results. Record attribution limits.

| Metric | Why it matters | Typical source |
|---|---|---|
| First meaningful response time | Inquiry handling speed | Channel + CRM timestamps |
| Lead-to-qualified rate | Qualification effectiveness | Pipeline stage evidence |
| Qualified-to-booked rate | Appointment conversion | Verified appointments |
| Workflow completion rate | Execution reliability | Outcome records |
| Exception and error rate | Operational friction | Exception outcomes + approvals |
| Verified value − operating cost | Economic impact | Revenue attribution + execution cost |

### Operator scoreboard (internal)

Aligns with [operating-leverage.md](../roadmap/operating-leverage.md):

- Human minutes per successful booking  
- Cost per successful mission / workflow completion  
- Intervention rate (human gates / exceptions)  
- Gross margin on the engagement  

### What we do **not** report as success

- Tool-call counts  
- Tokens consumed (except as cost input)  
- “Agent ran” without verified business state change  
- Unattributed revenue stories  

---

## 7. Sales process (outcome-first)

```text
Attention / referral
  → AION Business Audit (fit + baseline)
  → Outcome promise + process contract
  → Scoped deploy of Revenue Capture & Appointment OS
  → Acceptance tests + pilot measurement window
  → Operate / optimize retainer
  → Expand capabilities
```

### Discovery questions (minimum)

1. Where do inquiries enter today, and what is first-response time?  
2. What share of leads become qualified appointments?  
3. Which systems are systems of record (GHL, calendar, phone, other)?  
4. Which actions may run autonomously vs require human approval?  
5. What baseline numbers can we verify from the last 30–90 days?  
6. Who owns exceptions during business hours / after hours?  

### Sales rules

- Lead with the business bottleneck and economic cost of inaction.  
- Demonstrate tools only after the process and success measures are clear.  
- Never promise % lifts without a measured baseline.  
- Separate strategy/assessment fees from implementation and from operating cost.  

Primary CTA remains: **Book an AION Business Audit.**

---

## 8. FDE training path (AION Digital Empire)

Train Forward Deployment Engineers to ship **governed outcomes**, not demos of
tool calls.

| Module | FDE must be able to |
|---|---|
| Diagnose | Map inquiry → booking bottlenecks; quantify baseline |
| Contract | Write a business-process contract with acceptance tests |
| Configure | Encode tenant rules, qualifications, messaging, calendars |
| Govern | Set Observe / Assist / Execute tiers, approvals, kill paths |
| Integrate | Wire only approved capabilities through the Execution Gateway |
| Verify | Prove appointment + CRM state; record Outcome + cost |
| Operate | Triage exceptions, tune rules, report scorecard honestly |
| Expand | Propose next capability only after verified value |

Community curriculum should prefer case studies from appointment-driven SMBs
and reuse this blueprint’s scorecard language.

---

## 9. Delivery operating model

### Process contract (required artifact per client)

Every flagship engagement ships a short process contract containing:

- Outcome promise (plain language)  
- In-scope channels and systems  
- Capability list (`serviceKey` / business operations)  
- Autonomy / approval matrix  
- Baseline metrics + measurement window  
- Acceptance tests  
- Exception ownership  
- Explicit non-goals  

### Acceptance tests (flagship minimum)

| ID | Test | Pass criteria |
|---|---|---|
| AT-1 | Inbound capture | Inquiry creates or matches a contact with audit trail |
| AT-2 | Dedupe | Known contact is not duplicated under defined identity rules |
| AT-3 | Qualification | Rules produce qualified / not-qualified / needs-human |
| AT-4 | Follow-up gate | Outbound message requires approval when policy says so |
| AT-5 | Booking verify | Appointment exists in calendar/CRM or exception is logged |
| AT-6 | Scorecard | Report includes the six outcome metrics for the window |
| AT-7 | Fail-closed | Denied / failed actions leave no silent partial writes |

### Two productivity sprints

Monday–Tuesday build cadence.

| Window | Focus | Exit criteria | Status |
|---|---|---|---|
| **Sprint 1 — Oct 12–13** | Commercial definition + architecture | Offer finalized; process contract; GHL capabilities; baseline method; acceptance tests; demo-client workflow design | **Definition locked** — [revenue-capture-appointment-os/](revenue-capture-appointment-os/) |
| **Sprint 2 — Oct 19–20** | Pilot implementation + measurement | Authorized actions integrated; approval + exception gates exercised; end-to-end outcome test path; pilot demonstration package ready | **Fake harness green** — `aion-runtime` `proof:rcaos-at` (AT-1…AT-7). Live Empire / external SMB not claimed. |

Production rollout stays gated on testing, permissions, and operational acceptance.
Fake AT green ≠ live client pilot.

---

## 10. One-page operating model

| Domain | Operating rule |
|---|---|
| **Philosophy** | Outcome-Based Systems Engineering |
| **Sell** | Measurable results (Revenue / Ops / Managed AI Infra) |
| **Flagship product** | Revenue Capture & Appointment OS |
| **Technical foundation** | Agent OS + Execution Gateway |
| **Measurement layer** | Revenue Copilot + Outcome records |
| **Unit of execution** | Business capability (not raw tool call) |
| **Security substrate** | TechOps Foundation → Secure AI Execution → Continuous Assurance |
| **Proof** | Baseline → acceptance tests → verified value − cost |
| **People system** | FDEs diagnose, configure, govern, verify, operate |
| **Moat** | Vertical workflows + implementation quality + verified results |

---

## 11. Immediate backlog (documentation → missions)

1. ~~Accept or amend ADR-014.~~ **Accepted** 2026-10-12 with Sprint 1 amendments.  
2. ~~Create mission for Appointment OS v1.~~ [MISSION-RCAOS-001](../missions/MISSION-RCAOS-001-revenue-capture-appointment-os.md).  
3. ~~Lock Sprint 1 pack.~~ [revenue-capture-appointment-os/](revenue-capture-appointment-os/).  
4. ~~**Sprint 2 fake path:** implement D0–D7; AT-1…AT-7.~~ Done in `aion-runtime` (`proof:rcaos-at`).  
5. Live Empire tenant re-prove (optional) and external SMB pilot selection.  
6. Publish external messaging updates (site, bios, deck) after fee terms are set.  
7. Wire scorecard fields to durable Outcome / analytics contracts in `aion-data` as gaps appear.  
8. Align FDE / Revenue Academy modules to this blueprint’s language.  

Linear anchors: **AIO-8** (standardized delivery), **AIO-70** (Revenue Copilot Gate A), **OL-001**.

---

## 12. Non-goals

- Building a generic “agent marketplace” or another automation SaaS  
- Competing on connector count  
- Promising autonomy levels as the product  
- Claiming production readiness for untested vendor MCP paths  
- Replacing TechOps security work with outcome marketing  

---

## Revision history

| Date | Change |
|---|---|
| 2026-10-11 | Initial blueprint from Outcome-Based Systems Engineering strategic direction |
| 2026-10-12 | ADR-014 Accepted; Sprint 1 pack locked; MISSION-RCAOS-001 Defined |
