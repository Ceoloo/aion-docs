# ADR-014: Outcome-Based Systems Engineering as operating philosophy

- **Status:** Proposed
- **Date:** 2026-10-11
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

## Consequences

### Positive

- Aligns sales, delivery, architecture, and training on one scorecard language.
- Clarifies defensibility beyond orchestration.
- Gives a concrete flagship process for the next two productivity sprints.
- Preserves TechOps as the security substrate rather than conflating it with
  revenue packaging.

### Negative

- Messaging must be updated carefully so Sep 2025 consulting materials and this
  model do not contradict each other in market-facing copy.
- Outcome attribution is hard; teams must record limitations instead of
  inflating ROI stories.
- Capability-centric execution requires more product discipline than ad hoc
  agent scripts — short-term delivery may feel slower.

## Implementation Notes

- Treat the Oct 12–13 and Oct 19–20 sprint plan in the blueprint as **proposed
  targets**, not completed milestones.
- Prefer extending governed GHL / Execution Gateway paths over new substrate.
- Encode new work as missions with acceptance tests before production claims.
- Update Notion Offer Catalog and external site only after fee/terms owners act.

## Follow-up Decisions

- Founder acceptance of this ADR (Proposed → Accepted) and any fee-card decisions.
- Mission charter for Revenue Capture & Appointment OS v1 (demo client).
- Whether Business Audit is free fit conversation vs paid diagnostic as default.
- Which business capabilities are first-class `serviceKey`s vs product-local
  workflows (promotion under the platformization rule).
