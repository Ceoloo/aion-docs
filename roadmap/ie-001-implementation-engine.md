# IE-001 — Implementation Engine

**One client, one repeatable implementation, one live workflow.**

The Implementation Engine coordinates CRM, tenant setup, the execution
platform, and delivery process. It is **not** another runtime.

Immutable platform baseline remains
[`execution-platform-v0.2.0`](../releases/execution-platform-v0.2.0.manifest.json)
(and any later bootable Runtime tag such as `execution-platform-v0.2.1` for
ops). IE-001 is a **client-delivery** milestone on top of that substrate.

---

## Repo boundaries (confirmed against code)

| Repo | Owns |
|---|---|
| **aion-core** | `ImplementationCase` contracts, status transitions, rule-based qualification, blueprint draft helpers |
| **aion-data** | Tenant-scoped persistence (`implementation_cases` migration + repository) |
| **aion-runtime** | `/v1/implementations*` HTTP surface (thin gateway over Core + Data) |
| **aion-products** | Implementation workspace inside existing Operator Console (`workforce-control`) |
| **aion-docs** | This contract, templates, acceptance criteria, runbooks |
| **aion-infra** | Only if deploy/env secrets are required (none for first slice) |

**Correction to the original proposal:** Core must own contracts (same pattern as
M007–M009). Console never talks to Data directly — Runtime is the only path.

No new repo. No standalone app. Not `aion-desks`.

---

## Authoritative record — ImplementationCase

| Record | Controls |
|---|---|
| Intake | Business context, bottlenecks, access readiness, baseline |
| Package recommendation | Selected offer/version, rationale, exclusions, approval |
| Solution blueprint | Target workflow, integrations, permissions, KPIs, rollout |
| Provisioning checklist | Setup steps, dependencies, verification, recovery |
| Acceptance record | *(later)* Test results, client sign-off, activation |
| Success plan | *(in blueprint)* 30/60/90 + reporting cadence |

Every case has: `clientRef`, accountable `ownerId`, `commercialStatus`,
`deliveryStatus`, `nextAction`, `blockers`, `evidenceLinks`.

**Commercial ≠ technical.** Signed/paid does not imply activation-ready.

---

## Qualification (explainable first)

Rule map (`ie001-qualification-v1`):

| Primary bottleneck | Starting package |
|---|---|
| Leads lost inquiry → follow-up | Revenue OS |
| Intake / handoffs / updates inconsistent | Client Ops OS |
| Repetitive admin consumes owner time | AI Workforce Setup |
| Booking / intake / delivery / reviews disconnected | Service Business Automation Stack |

Readiness gates: named owner, measurable problem, access ready, approved scope,
feasible delivery capacity.

Outcomes: `recommend_package` | `request_discovery` | `hold_missing_inputs` |
`decline_out_of_scope`.

**Model access must not block intake or package selection.** GHL and model
steps appear on the provisioning checklist as **blocked** until verified.

Service Business Automation Stack is a reusable catalog configuration — not an
unpriced fifth offer.

---

## First engineering slice (ship now)

Operator flow:

1. Create ImplementationCase  
2. Complete Intake  
3. Review rule-based Package Recommendation (human may note override)  
4. Edit and **approve** versioned Blueprint  

APIs (tenant header required):

- `POST /v1/implementations`
- `GET /v1/implementations`
- `GET /v1/implementations/:caseId`
- `POST /v1/implementations/:caseId/intake`
- `POST /v1/implementations/:caseId/blueprint` (draft / edit)
- `POST /v1/implementations/:caseId/blueprint/approve`

Console routes:

- `/implementations`
- `/implementations/new`
- `/implementations/:caseId`

### Build now vs wait

**Build now:** intake, rule qualification, versioned blueprint template,
operator provisioning checklist (visible statuses), Revenue OS-oriented draft,
acceptance checklist fields on blueprint, commercial→delivery handoff fields.

**Wait for live integration proof:** automatic provisioning, autonomous
customer messaging, automatic workflow activation, expansion → commercial
actions.

Manual execution is fine; **invisible** manual execution is not — record who
and evidence.

---

## IE-001 acceptance gate

Passes when **one new client**:

1. Enters via standard commercial handoff  
2. Receives approved package + blueprint  
3. Is provisioned without undocumented setup steps  
4. Passes tenant, permission, failure, and recovery checks  
5. Runs one approved live workflow with recorded outcome + cost  
6. Receives a report (results, exceptions, next actions)  

AION internal rehearsal validates process but **does not** substitute for the
new-client milestone. Provisioning tasks / synthetic tests **do not** count
toward OL-001 real revenue missions.

---

## Explicit non-goals (this slice)

- Automatic GHL provisioning  
- Autonomous messaging  
- Auto-activating workflows  
- New orchestration framework or second runtime  
- CRM fork inside aion-data (GHL remains CRM truth)
