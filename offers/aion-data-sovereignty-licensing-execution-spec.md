# AION Data Sovereignty & Licensing — Execution Specification

- **Status:** Proposed commercial validation (approved to sprint; not production)
- **Date:** 2026-10-20
- **Linear project:** [P-AIO-9](https://linear.app/aion-empire/project/aion-data-sovereignty-and-licensing-validation-sprint-a23e98149964)
- **Mission:** [DSL-001](../missions/MISSION-DSL-001-data-sovereignty-licensing-validation.md)
- **Doctrine:** [ADR-016](../adr/ADR-016-sovereign-intelligence-doctrine.md) · [Sovereign Intelligence Doctrine](../architecture/sovereign-intelligence-doctrine.md)
- **Owners:** AION Systems (product) · TechOps Security Infrastructure Layer (assurance) · Digital Empire (acquisition)

## 1. Purpose

Convert the 2026-10-20 Strategic Intelligence Report into a **Linear-ready
execution plan** that AION can run without building a data marketplace, scraping
network, or commission-only brokerage.

**Working product name:** AION Data Sovereignty & Licensing Infrastructure

**Operating principle:** Help businesses own their intelligence first. Help
them monetize it second. Never trade away their control merely to secure a
commission.

## 2. Decision record (what Holding approved)

| Decision | Status |
|---|---|
| Small commercial validation sprint under AION Systems | **Approve** |
| Paid Sovereign Data Opportunity Audit as first offer | **Approve** |
| Public / permitted prospect metadata only; no client dataset transfers in Phase 0–1 | **Approve** |
| Full-scale marketplace, automated brokerage, high-volume scraping | **Defer** |
| Sharing AION-owned proprietary workflows / customer datasets for commissions | **Block** |

## 3. Business model under test

Three connected revenue streams. Referral commissions are **upside only**.

| Stream | Customer receives | Initial price hypothesis |
|---|---|---|
| **Data Asset Intelligence** | Inventory, classification, buyer-fit without transferring records | Audit $1,500–$3,000 |
| **Sovereign Data Infrastructure** | Private storage, local AI processing, access policies, audit | Build $15,000–$40,000+ |
| **Data Licensing Governance** | Rights review, disclosure control, contract tracking, transfer oversight | Readiness $5,000–$12,000; retainers $1,000–$5,000/mo |

Illustrative monthly mix (scenario, not forecast): 4 × $2,500 audits + 1 ×
$15,000 build + 2 × $1,500 retainers ≈ **$28,000** gross before CAC/expenses.

The paid audit must be valuable even if the customer never licenses anything.

## 4. Scope boundaries

### In scope (90 days)

1. Buyer channel validation (micro1 referral terms + Polyshares licensing program)
2. Permitted-source Prospect Intelligence Engine (metadata only)
3. Client-boundary Data Asset Registry prototype
4. Licensing gates (consent, rights, privacy, human release)
5. Two pilot audits; ≥1 paid if demand holds
6. Scorecard + SOPs for repeatability

### Out of scope

- Autonomous production data transfers
- Google Maps / bulk scraper acquisition
- Gift-card or other inducements to referred companies (prohibited by micro1 terms)
- Law firm case files, patient records, private customer conversations as pilot datasets
- New standalone company or duplicate CRM outside `revenue_*` canonical model
- Blocking Runtime GE-001 critical path

## 5. Security boundaries (mandatory)

| Boundary | Allowed | Forbidden |
|---|---|---|
| **A — Public prospect intelligence** | Permitted public / licensed directories; local scoring | Scraping against provider ToS; storing secrets in prospect DB |
| **B — Protected client intelligence** | Client-controlled workspace; local processing | Public AI APIs; shared third-party workspaces; uncontrolled cloud copies |
| **C — Authorized licensing** | Explicit approval, contract, logged transfer to named recipient | Bulk referral without consent; reversible-sharing promises |

Even a valid license transfers control. AION must not promise fully reversible
model training once knowledge is incorporated.

Until Runtime production certification (GE-001) is verified, **all release and
transfer steps are manual**.

## 6. Architecture components

Target architecture plugs into AION’s governed execution model — it does not
duplicate permissions, memory, or audit.

| Component | Responsibility | Suggested implementation |
|---|---|---|
| Prospect Intelligence | Public company discovery & research | Python workers + licensed data sources |
| Qualification Service | Fit scoring & evidence checking | Local rules + self-hosted LLM |
| Data Asset Registry | Datasets, owners, classifications | Self-hosted PostgreSQL |
| Rights Registry | Contracts, consent, restrictions | Encrypted records + signed approvals |
| Private Processing | Redaction, transformation, validation | Locally hosted pipeline |
| AION Runtime | Governed execution & audit | Existing gateway (manual until certified) |
| Approval Console | Owner / legal / security review | AION Workspace |
| Outcome Ledger | Deals, referrals, payments | Existing `revenue_*` integration |

### Domain entities (client boundary)

Isolated from hosted CRM sync except minimal permitted status:

- `data_assets`
- `rights_reviews`
- `license_opportunities`
- `release_approvals`
- `transfer_receipts`

### Pipeline (seven stages)

1. Identify target industries  
2. Build company datasets (permitted sources)  
3. Qualify and score  
4. Find decision-makers  
5. Run targeted outreach (CAN-SPAM compliant)  
6. Refer and evaluate (consent first)  
7. Track agreements and fees (collections, not promises)

### Flow gate

```
Public metadata → Research → Score → Discovery
  → Owner authorizes assessment?
       No  → Close / nurture (no data access)
       Yes → Client private workspace
         → Rights + inventory
         → Legal/privacy pass?
              No  → Remediate / reject
              Yes → Local approved processing
                → Quality + privacy evaluation
                → Human release approval?
                     No  → Keep in client boundary
                     Yes → Contract-bound transfer gateway
                       → Receipt + audit + outcome tracking
```

## 7. Licensing gates (acceptance checklist)

Every engagement must pass before any referral or transfer:

| Gate | Rule |
|---|---|
| G1 Ownership | Document who owns / controls rights to each dataset class |
| G2 Third-party confidentiality | Review client, vendor, employee, customer agreements |
| G3 Minimization | Collect metadata before requesting actual records |
| G4 De-identification | Residual identifier and re-identification risk tested |
| G5 License terms | Duration, exclusivity, sublicensing, training rights, termination reviewed |
| G6 Marketing compliance | Suppression lists, honest claims, CAN-SPAM / applicable law |
| G7 Security | Encryption, access restriction, decision logging, transfer governance |
| G8 Human authorization | Explicit approval for every release |

**Pilot industry:** US construction or technical-service companies with ≥30
employees. Prefer internally developed SOPs and lower-risk operational
documents.

**Buyer notes (as of research 2026-10-20):**

- micro1: referral requires authorized consent; no inducements; $50k is not
  automatic; fallback may be 7.5% when unspecified; clawbacks possible.
- Polyshares: advertised licensing ranges $100k–$2M to businesses; exclusive by
  default unless negotiated; independent referrer commission terms not verified.

Forecast commissions only after written confirmation of applicable terms.

## 8. First offer — Sovereign Data Opportunity Audit

### Deliverables

1. Data inventory (classes, systems, volume proxies — not bulk export)
2. Sovereignty / risk classification
3. Buyer-fit assessment (if any)
4. Recommended next step: no license / readiness work / infrastructure / stop

### Pricing

$1,500–$3,000 starting hypothesis. Value stands without a licensing deal.

### Delivery SOP (minimum)

1. Signed engagement + confidentiality  
2. Metadata questionnaire only in first session  
3. On-site or client-controlled review for sample records if authorized  
4. Written report + gate checklist  
5. Optional referral path only after G1–G8 and signed consent  

## 9. 90-day roadmap & acceptance milestones

| Milestone | Window | Exit gate |
|---|---|---|
| **M1 Buyer & legal feasibility** | Days 1–14 (→ 2026-11-02) | One verified buyer channel; legal screening rules; approved offer |
| **M2 Legitimate prospect pipeline** | Days 15–30 (→ 2026-11-18) | 100 researched companies; 5–10 discovery meetings; measured funnel |
| **M3 Paid audit launch** | Days 31–60 (→ 2026-12-18) | ≥1 paying audit **or** documented repositioning evidence; registry prototype |
| **M4 Repeatable delivery** | Days 61–90 (→ 2027-01-17) | Licensing-readiness assessment **or** private infra deploy; SOP + case study; collected service revenue |

### Scorecard (expansion decisions)

| Metric | Proves |
|---|---|
| Qualified businesses / researched | Targeting quality |
| Discovery meetings / contacts reached | Message-to-market fit |
| Eligible datasets / audits completed | Real opportunity |
| Paid audits / proposals sent | Service demand |
| Verified commissions collected | Referral economics |
| Gross margin per engagement | Delivery profitability |
| Security / compliance exceptions | Operational risk |
| Time assessment → delivered outcome | Repeatability |

## 10. Linear work breakdown

Parent project: **[P-AIO-9](https://linear.app/aion-empire/project/aion-data-sovereignty-and-licensing-validation-sprint-a23e98149964)** · Parent issue: **[AIO-89](https://linear.app/aion-empire/issue/AIO-89/dsl-001-data-sovereignty-and-licensing-validation-sprint-parent)** · Doc: [Execution Spec (Linear)](https://linear.app/aion-empire/document/execution-spec-data-sovereignty-and-licensing-dsl-001-055665848303)

| Issue | Theme | Milestone | Primary owner |
|---|---|---|---|
| [AIO-90](https://linear.app/aion-empire/issue/AIO-90) | Buyer channel contracts & attribution memo | M1 | Digital Empire + counsel |
| [AIO-91](https://linear.app/aion-empire/issue/AIO-91) | Legal screening rules (pilot industry) | M1 | TechOps / Security |
| [AIO-92](https://linear.app/aion-empire/issue/AIO-92) | Offer one-pager + pricing hypothesis | M1 | AION Systems |
| [AIO-93](https://linear.app/aion-empire/issue/AIO-93) | Permitted-source research playbook | M2 | Digital Empire |
| [AIO-94](https://linear.app/aion-empire/issue/AIO-94) | Prospect Intelligence Engine v0 (100 companies) | M2 | AION Systems |
| [AIO-95](https://linear.app/aion-empire/issue/AIO-95) | Qualification scoring rubric | M2 | AION Systems |
| [AIO-96](https://linear.app/aion-empire/issue/AIO-96) | CAN-SPAM outreach kit (no inducements) | M2 | Digital Empire |
| [AIO-97](https://linear.app/aion-empire/issue/AIO-97) | Data Asset Registry prototype schema | M3 | Data / Systems |
| [AIO-98](https://linear.app/aion-empire/issue/AIO-98) | Audit delivery template + gate checklist | M3 | Systems + TechOps |
| [AIO-99](https://linear.app/aion-empire/issue/AIO-99) | Two pilot audits; bill at least one | M3 | Digital Empire delivery |
| [AIO-100](https://linear.app/aion-empire/issue/AIO-100) | Rights Registry + release approval workflow (manual) | M4 | Systems + TechOps |
| [AIO-101](https://linear.app/aion-empire/issue/AIO-101) | Licensing-readiness assessment **or** infra pilot | M4 | Systems + TechOps |
| [AIO-102](https://linear.app/aion-empire/issue/AIO-102) | Case study, SOP pack, expansion go/no-go | M4 | Holding review |

## 11. Dependencies & risks

| Dependency / risk | Mitigation |
|---|---|
| Runtime GE-001 not certified | Manual approvals/transfers only |
| Buyer terms change | Re-verify before each referral; do not forecast on ads |
| Consent / attribution disputes | Written authorized consent; pipeline-check before submit |
| Privacy / Delete Act / HIPAA scope creep | Stay on operational SOPs; legal classification before any personal-data brokerage |
| Distraction from Pilot-to-Revenue / GE-001 | Cap staffing; no marketplace build |

## 12. Ownership matrix

| Division | Responsibility |
|---|---|
| AION Holding | Capital, risk appetite, expansion approval |
| AION Systems | Architecture, registry, qualification, Runtime integration |
| TechOps Security Infrastructure Layer | Security reviews, privacy controls, local infrastructure |
| Digital Empire | Positioning, lead sourcing, sales — **not** independent brokerage infra |

## 13. Definition of done (sprint)

The validation sprint is complete when:

1. M1–M4 exit gates are recorded with evidence links in Linear  
2. Scorecard has measured values (zeros allowed if explained)  
3. Holding has a written expand / iterate / kill decision  
4. No unauthorized client data left AION or client boundaries  
5. Runtime critical path was not delayed by this experiment  

---

Research baseline: Strategic Intelligence Report, 2026-10-20. Commercial
program terms change; counsel reviews contracts before any transfer or pilot.
