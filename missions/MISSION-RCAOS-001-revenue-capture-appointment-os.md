# Mission: Revenue Capture & Appointment OS v1 (demo client)

- **Mission ID:** MISSION-RCAOS-001
- **Owner:** CEO LOO (decision) · Sprint FDE (delivery)
- **Status:** Build
- **Date:** 2026-10-12
- **Updated:** 2026-10-11 — Sprint 2 fake harness AT-1…AT-7 green (`proof:rcaos-at`)

## Problem

Appointment-driven SMBs lose revenue when inquiries are slow to answer, poorly
qualified, or never booked — and when no one can measure where the process
breaks. Tool-access demos do not fix that.

## Target User

AION Empire operators (customer-zero) first; then electrical / home-service SMB
owners who need measurable inquiry → booking follow-through.

## Expected Outcome

A governed end-to-end path where every demo inquiry reaches a terminal Outcome
(booked with verify evidence, disqualified, or exception with owner), with
scorecard metrics and cost — without autonomous send or ungoverned writes.

## Scope

- Lock and implement Revenue Capture & Appointment OS v1 against Empire GHL
- Business capability `qualify_lead_and_prepare_booking` composing catalog keys
- Process contract, baseline method, acceptance tests AT-1…AT-7
- Align with Secure Automation `lead-to-appointment-v1`

## Non-Goals

- Enabling `crm.message.send` or `crm.appointment.create` in this mission
- External SMB production rollout
- Building a new platform or agent marketplace
- Revenue Copilot Gate A call intelligence (tracked under AIO-70)
- Guaranteed percentage conversion lifts

## Success Metrics

- Sprint 1: commercial + architecture artifacts locked (done when pack lands)
- Sprint 2: AT-1…AT-7 exercised on demo tenant with audit IDs
- Scorecard produced with explicit baseline limitations
- Zero silent partial writes on failure / ambiguous timeout paths

## Architecture Impact

| Repo | Impact |
|---|---|
| `aion-docs` | Offer pack, ADR-014, this mission |
| `aion-core` | Compose existing catalog / Secure Automation workflow — no new substrate required for design |
| `aion-runtime` | Gateway executions, GHL adapter, proofs |
| `aion-data` | Outcome / cost records for scorecard |
| `aion-products` | Optional Copilot/Console surfaces for reporting — not blocking demo path |

ADRs: [ADR-014](../adr/ADR-014-outcome-based-systems-engineering.md) (Accepted).

## Data Requirements

- CRM contact / opportunity / appointment reads (GHL)
- Execution + ExternalSideEffect + Outcome records
- Cohort labels: `demo_rcaos_v1`, `productionEconomic=false` for synthetic
- No new canonical entity required for Sprint 1 design; Sprint 2 may add outcome
  projection fields if gaps appear

## Security Considerations

- Least privilege via Execution Gateway
- R2 approvals on opportunity mutations and message drafts
- Demo namespace isolation from real prospects
- Fail-closed on missing credentials / disabled capabilities
- TechOps substrate remains prerequisite for external clients

## Release Gate

- AT-1…AT-7 green on demo tenant **or** documented waiver with exception Outcome
- No `crm.message.send` / `crm.appointment.create` enabled without separate acceptance
- Evidence pack: execution IDs, approval IDs, appointment verify or exception

## Validation Gate

- Scorecard compares pilot observed vs baseline with limitations stated
- Operator can explain every exception in the cohort
- Decision: continue to external SMB pilot, iterate rules, or kill scope creep

## Learning Requirements

- Where humans still intervene (and why)
- Which qualification rules cause false qualify / false reject
- Cost per terminal Outcome
- Whether appointment verify-via-read is sufficient before enabling appointment writes

## Sprint linkage

| Sprint | Mission stage |
|---|---|
| Oct 12–13 definition lock | Defined → Architecture (docs) |
| Sprint 2 fake harness | Build — AT-1…AT-7 green on FakeGhlBackend (`aion-runtime` `proof:rcaos-at`) |
| Next | [Pilot-to-Revenue Program](../offers/aion-pilot-to-revenue-program.md) — No Shorts design partner (proposed); GE-001 (AIO-35 → AIO-37) **and** live AION Systems customer-zero RCAOS proof are **non-waivable** before any external customer-facing writes |
