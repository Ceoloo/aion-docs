# ADR-014: Outcome-Based Systems Engineering as operating philosophy

- **Status:** Accepted
- **Date:** 2026-10-11
- **Accepted:** 2026-10-12
- **Decision Owners:** CEO LOO

## Context

AI vendors and SaaS platforms are rapidly exposing business software to agents
(MCP servers, headless APIs, native agent experiences). Integration access is
commoditizing. At the same time, AION already has a governed execution platform
(Agent OS / control plane, Execution Gateway, Outcome records, Revenue Copilot)
and a commercial history that has shifted from build-led packaging to consulting
positioning (2026-09-25 Notion Offer Catalog).

The open question is what AION should compete on: tool connectivity and agent
demos, consulting advice alone, or **verified business outcomes produced by
governed systems**.

Strategic risk: vendors will also ship orchestration and native automations.
Owning “an agent layer” is not durable by itself.

Sprint 1 (Oct 12–13) required amendments that turn the philosophy into a locked
flagship definition without claiming production pilot completion.

## Decision

We will adopt **Outcome-Based Systems Engineering** as the central operating
philosophy of AION Systems:

1. **Positioning:** Design, deploy, and operate intelligent business systems that
   turn existing software into measurable results.
2. **Commercial pillars:** Revenue Systems, Operations Systems, and Managed AI
   Infrastructure — clients buy results; AION owns the producing process.
3. **Technical posture:** Keep Agent OS as the foundation, Revenue Copilot as the
   business-facing measurement layer, and business **capabilities** (not raw tool
   calls) as the unit of execution.
4. **Flagship offer:** Revenue Capture & Appointment OS for appointment-driven
   SMBs (contractors / home services first).
5. **Proof standard:** Baseline with client data; measure outcomes (response time,
   qualification, booking, completion, exceptions, verified value − cost); do not
   treat tool-call activity as success.
6. **People system:** Train Forward Deployment Engineers to diagnose bottlenecks,
   configure governed workflows, test outcomes, and operate client systems.

### Amendments accepted 2026-10-12

7. **Business Audit default:** Free fit conversation for qualified
   appointment-driven SMBs. Escalate to a **paid diagnostic** when discovery
   requires structured data pull / scorecard work beyond a ~60-minute fit call.
8. **Capability vs `serviceKey`:** `qualify_lead_and_prepare_booking` is a
   product / Secure Automation workflow that **composes** catalog `serviceKey`s
   (aligned with `lead-to-appointment-v1`). It is **not** promoted to a
   first-class platform `serviceKey` until the platformization rule is met.
9. **v1 execution honesty:** Appointment **booking** is human-operated;
   `crm.appointment.create` and `crm.message.send` stay deferred. Success is
   verified via `crm.appointment.read` (or an explicit exception Outcome).
10. **Sprint 1 pack is authoritative** for the flagship commercial definition:
    [offers/revenue-capture-appointment-os/](../offers/revenue-capture-appointment-os/).
11. **Demo client:** AION Empire customer-zero (`aion-systems` GHL tenant) before
    any external SMB pilot.

Authoritative packaging: [offers/aion-outcome-based-services-blueprint.md](../offers/aion-outcome-based-services-blueprint.md).

## Alternatives Considered

- **Remain a pure AI consulting firm** — Rejected as the sole identity. Consulting
  remains the front door, but without owned outcome systems AION does not
  compound reusable delivery IP or verified operational results.
- **Compete on connector / MCP coverage** — Rejected. Vendor access is
  commoditizing; governance and outcomes are the scarce goods.
- **Build a new horizontal agent platform** — Rejected for now. Existing
  architecture is sufficient; platformization follows proven missions
  ([platform-maturity.md](../roadmap/platform-maturity.md)).
- **Sell autonomy level as the product** — Rejected. Reliable outcomes under
  governance remain the goal ([system-overview.md](../architecture/system-overview.md)).
- **Enable appointment write + autonomous send in v1** — Rejected for the
  flagship lock. Catalog already marks these inactive/deferred; v1 proves the
  governed process with human booking and draft-only messaging.

## Consequences

### Positive

- Aligns sales, delivery, architecture, and training on one scorecard language.
- Clarifies defensibility beyond orchestration.
- Gives a concrete flagship process with locked Sprint 1 artifacts and a
  Defined mission ([MISSION-RCAOS-001](../missions/MISSION-RCAOS-001-revenue-capture-appointment-os.md)).
- Preserves TechOps as the security substrate rather than conflating it with
  revenue packaging.
- Avoids over-claiming calendar/send automation before certification.

### Negative

- Messaging must be updated carefully so Sep 2025 consulting materials and this
  model do not contradict each other in market-facing copy.
- Outcome attribution is hard; teams must record limitations instead of
  inflating ROI stories.
- Capability-centric execution requires more product discipline than ad hoc
  agent scripts — short-term delivery may feel slower.
- Human booking + draft-only messaging limits the “fully automated” story in v1.

## Implementation Notes

- Prefer extending governed GHL / Execution Gateway paths and
  `lead-to-appointment-v1` over new substrate.
- Sprint 1 definition lock lives under
  `offers/revenue-capture-appointment-os/` (process contract, GHL capabilities,
  baseline method, demo-client workflow).
- Sprint 2 (Oct 19–20) implements and evidences AT-1…AT-7 on the demo tenant —
  still not an external production rollout.
- Encode work through [MISSION-RCAOS-001](../missions/MISSION-RCAOS-001-revenue-capture-appointment-os.md).
- Update Notion Offer Catalog and external site only after fee/terms owners act.

## Follow-up Decisions

- Fee card / package prices (founder-owned; not set by this ADR).
- When (if ever) to certify `crm.appointment.create` and `crm.message.send` for
  the flagship offer — requires separate acceptance evidence.
- External SMB pilot client selection after demo AT pack is green.
- Promotion of `qualify_lead_and_prepare_booking` to a platform `serviceKey`
  under the platformization rule.
