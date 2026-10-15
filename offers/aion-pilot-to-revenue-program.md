# AION Pilot-to-Revenue Program

**Status:** Proposed operating priority (updated 2026-10-12)  
**Decision:** Move from **Building Systems → Proving Outcomes**  
**Track chosen:** Launch first client pilot (design partner) before broad acquisition scaling  
**Linear:** [AIO-71](https://linear.app/aion-empire/issue/AIO-71/p0-pilot-to-revenue-no-shorts-electrical-design-partner-rcaos) (blocked by AIO-35, AIO-37)  
**Flagship process:** [Revenue Capture & Appointment OS](revenue-capture-appointment-os/)  
**Operating philosophy:** [Outcome-Based Services Blueprint](aion-outcome-based-services-blueprint.md) · [ADR-014](../adr/ADR-014-outcome-based-systems-engineering.md)  
**Readiness:** [production-readiness-2026-10-12](../.aion/research/production-readiness-2026-10-12.md) — GE-001 + live customer-zero RCAOS are **non-waivable** before external writes

This program turns the completed blueprint into a **verified, revenue-producing
client deployment**. Positioning, architecture, and service model are already
documented. The missing asset is **evidence that a business will pay for the
outcome**.

---

## 1. Objective

Deploy **one complete revenue workflow**, prove it works, measure financial
impact against a client baseline, and convert the implementation into a
**repeatable service**.

Success is not “agents ran.” Success is:

1. A signed/paid pilot with written success criteria  
2. Governed inquiry → booking path running on the client’s systems  
3. Outcome metrics vs baseline  
4. Path to case study + deployment template + managed-service agreement  

---

## 2. Design partner — No Shorts Electrical

| Field | Value |
|---|---|
| Target | **No Shorts Electrical** (electrical contractor / appointment-driven SMB) |
| Why first | Existing relationship + demo concept; fits initial vertical |
| Evidence state | Demo / relationship discussed in Notion (content shot list, Operating Sync). **Signed scope, payment, and launch are not established** as of this charter |
| Contact / demo notes | Notion: [No Shorts Electrical demo walkthrough](https://app.notion.com/p/3e25b5fa4f8f81e99962ed9056594b9f) — confirm accessibility before any filming or live CRM work |

Until a paid pilot agreement is signed, treat No Shorts as **proposed design
partner**, not an active production tenant.

---

## 3. Technical gate (must clear before live customer-facing actions)

Live customer-facing CRM/messaging/calendar actions require the governed
execution production path to be **verified**, not only fake-harness green.

| Gate | Linear / doc | Status (as of charter) | Blocks |
|---|---|---|---|
| Maintainer deploy Runtime tip + capture production identity | [AIO-35](https://linear.app/aion-empire/issue/AIO-35/p0-maintainer-deploy-runtime-57-and-capture-production-identity) | **Todo** | Serving digest / health identity |
| GE-001 live certify on fresh low-risk real record | [AIO-37](https://linear.app/aion-empire/issue/AIO-37/p0-certify-ge-001-on-a-fresh-low-risk-real-record) | **Todo / UNVERIFIED** | Live governed write + restart + economics evidence |
| Governed execution checklist | [roadmap/governed-execution-certify.md](../roadmap/governed-execution-certify.md) | Open | Order: deploy → Annfiera cleanup → GE-001 |
| Candidate release pin | [releases/governed-execution-candidate.manifest.json](../releases/governed-execution-candidate.manifest.json) | `result` UNVERIFIED | Do not treat as PASS |
| Fake RCAOS AT-1…AT-7 | `aion-runtime` `proof:rcaos-at` | Harness path (docs/runtime PRs) | Unblocks **prepare/test**, **not** live customer send/book automation |

**Rule:** Commercial scoping, baseline collection, GHL read-only audit, and
**draft-only** configuration may proceed in parallel with GE-001.

**Non-waivable live gates (before any external customer-facing writes/sends):**

1. **GE-001 PASS** (AIO-35 → AIO-37) on the pinned `20fe3a4` candidate — no
   owner waiver for live writes.
2. **Live AION Systems customer-zero RCAOS proof** — re-prove the revenue
   workflow against the internal customer-zero tenant before enabling any
   No Shorts (or other external) write. Synthetic AT-1…AT-7 green does **not**
   satisfy this gate.

Read-only discovery and commercial SOW work may continue; live external writes
may not.

---

## 4. What we already have (do not rebuild)

| Asset | Role in pilot |
|---|---|
| Outcome-Based Services Blueprint + ADR-014 | Commercial + technical philosophy |
| RCAOS process contract / baseline method / GHL caps | Pilot contract templates |
| `lead-to-appointment-v1` + Execution Gateway | Governed CRM path |
| Fake `proof:rcaos-at` (AT-1…AT-7) | Pre-flight regression before live |
| TechOps ladder | Security substrate for client digital labor |

---

## 5. 14-day execution plan (proposed targets)

These are **proposed calendar targets**, not completed milestones.

### Sprint A — Oct 12–13: Secure and scope the pilot

| # | Work | Done when |
|---|---|---|
| A1 | Confirm No Shorts as design partner (verbal + written intent) | Named owner + decision date recorded |
| A2 | Map inquiry → booking operations (channels, owners, after-hours) | Process map attached to process contract |
| A3 | Collect baseline metrics (30-day lookback preferred) | [baseline-method.md](revenue-capture-appointment-os/baseline-method.md) table filled |
| A4 | Audit integrations (GHL location, calendars, pipelines, messaging) | Integration checklist + gaps |
| A5 | Agree **paid pilot** + written success criteria | Signed or countersigned pilot SOW / order form |

**Commercial minimum for A5:** fee (or clearly valued exchange), measurement
window, in-scope capabilities, approval matrix, non-goals, termination.

### Mid-window — Oct 14–18: Prepare and test the deployment

| # | Work | Done when |
|---|---|---|
| B1 | Configure client GHL (demo/pilot pipeline stages, tags, isolation) | Config sheet |
| B2 | Implement / configure qualification rules for No Shorts | Ruleset ID + fixtures |
| B3 | Verify calendar behavior (human book + read-back) | Pass/fail notes |
| B4 | Test exception paths (needs_human, low confidence, deny) | AT-3 / AT-7 style cases on client sandbox or Empire staging |
| B5 | Prepare human approval controls (named approvers, R2/R3) | Approver roster |
| B6 | Re-run fake RCAOS pack; confirm GE-001 / AIO-35 status | Gate board updated |

### Sprint B — Oct 19–20: Controlled launch

| # | Work | Done when |
|---|---|---|
| C1 | Deployment readiness review (gates + checklist) | Go / no-go recorded |
| C2 | Live acceptance tests (critical cases) | Evidence pack IDs |
| C3 | Limited pilot launch **if authorized** | Cohort label + start timestamp |
| C4 | Begin collecting outcome evidence vs baseline | Daily scorecard stub |

**No-go** if GE-001 / production identity gates are still open, **or** if live
customer-zero RCAOS proof is still open, when the pilot would require live
customer-facing writes. In that case: continue prepare/test and commercial
scoping only — do not waive into external writes.

---

## 6. Acceptance conditions (proposed — not achieved)

| Acceptance metric | Initial target | Notes |
|---|---|---|
| End-to-end workflow tests | 100% of critical cases pass | Map to RCAOS AT-1…AT-7 + client-specific cases |
| CRM record consistency | No unresolved critical mismatches | Contact / opp / stage / note / task |
| Customer-facing actions | Authorized and auditable | Approval IDs + side-effect ledger |
| Appointment verification | Confirmed against actual calendar | Prefer read-back; document human book |
| Revenue impact | Measured against client baseline | Observation window required; no % lift promises pre-baseline |

Conversion improvement requires a **live observation period** after launch.
Do not claim ROI until the scorecard compares observed vs baseline with
attribution limitations stated.

---

## 7. After a verified pilot — three reusable assets

| Asset | Purpose |
|---|---|
| **Case study** | Documented problem → baseline → process → results → limits |
| **Deployment template** | Process contract + GHL config + ruleset + acceptance pack |
| **Managed-service agreement** | Operate / optimize retainer language + scorecard cadence |

Then sell the **same proven process** to the next 5–10 contractors without
rebuilding from scratch (configure, don’t reinvent).

---

## 8. Program scoreboard (internal)

| Metric | Why |
|---|---|
| Pilot signed / paid | Revenue validation signal |
| Days to first verified booking Outcome | Time-to-value |
| Critical AT pass rate | Execution reliability |
| Exception rate | Operational friction |
| Verified value − operating cost (pilot window) | Economic proof |
| Expansion / MSA conversion | Repeatability |

---

## 9. Decision record

| Choice | Decision |
|---|---|
| Philosophy | Outcome-Based Systems Engineering (ADR-014) |
| Next move | Pilot-to-Revenue — first design partner deployment |
| Acquisition vs pilot | **Pilot first** — a real deployment strengthens sales |
| First partner | No Shorts Electrical (proposed) |
| Product | Revenue Capture & Appointment OS |
| Live gate | GE-001 PASS (AIO-35 → AIO-37) **and** live customer-zero RCAOS proof — both non-waivable before external customer-facing writes |

---

## 10. Immediate checklist (this week)

- [ ] CEO LOO: confirm No Shorts outreach / pilot conversation owner  
- [ ] Draft paid pilot SOW using [process-contract.md](revenue-capture-appointment-os/process-contract.md)  
- [ ] Schedule baseline data pull (GHL export / interview)  
- [ ] Production maintainer: clear AIO-35 deploy identity  
- [ ] Schedule GE-001 (AIO-37) on fresh low-risk record  
- [ ] Keep fake `proof:rcaos-at` green on Runtime main  

---

## Revision history

| Date | Change |
|---|---|
| 2026-10-12 | Remove GE-001 owner-waiver; require live customer-zero RCAOS before external writes ([production-readiness-2026-10-12](../.aion/research/production-readiness-2026-10-12.md)) |
| 2026-10-11 | Initial Pilot-to-Revenue Program charter; No Shorts as proposed design partner; GE-001 hard gate |
