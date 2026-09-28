# Agent mission — GE-001 Governed Execution certify

```
MISSION ID:              GE-001
TITLE:                   Certify one clean GOVERNED EXECUTION revenue workflow
BUSINESS OUTCOME:        One release-pinned, restart-safe AION Systems workflow
                         with economic evidence — without contaminating client
                         CRM state — unlocking Mission-001 dataset collection
PRIMARY OWNER:           Runtime + Product (joint execution); Architect gate
SUPPORTING AGENTS:       Infra (release manifest + deploy), Data (RLS/TLS),
                         QA (acceptance evidence), Security (TLS + auth)
REVIEWERS:               Architect, QA, Security
REPOSITORY:              multi — runtime, products, infra, data, docs
BRANCH / WORKTREE:       deploy from pinned tips; docs on
                         cursor/production-readiness-2026-09-28-e738
CONTEXT:                 RES-002 certify-first accepted. OL-001 live run
                         contaminated Annfiera CRM with synthetic note.
                         Runtime #57 boundary merged (20fe3a4) but not
                         evidenced as serving image. Products main red;
                         #44 green/MERGEABLE. Last prod digest = Sep 22
                         10de0663. See production-readiness-2026-09-28.md.
OBJECTIVE:               Close the GOVERNED EXECUTION — VERIFIED gate for one
                         AION Systems revenue workflow using existing
                         Operator Loop + contracts; no new subsystems.
ACCEPTANCE CRITERIA:
  Order (do not reorder): green Products main → deploy+identify Runtime
  digest → clean only the identified Annfiera note (preserve incident
  ledger) → GE-001 on a fresh real record.
  1. Products #44 merged; Products main CI green — DONE (06eedbf9 / run 36488161210)
  2. Cross-repo release manifest published with Runtime image digest+SHA,
     Core/Data pins, migration set, Products SHA, Infra SHA, verified
     production identity (health/ready git_sha matches)
  3. Serving Runtime includes #57 synthetic-production boundary
  4. Contaminated Annfiera note cleaned per incident cleanup section
  5. Fresh low-risk real record used — not synthetic evidence on client contact
  6. Named human approval path + denial-without-side-effect path both proven
  7. Restart/resume under same rootExecutionId at governed pause
  8. Provider reconciliation scenario exercised before any retry after
     crash-after-provider-success
  9. Terminal outcome recorded with separate fields:
     provider expense | operator time | pipeline value | collected cash
     — Provider expense: actual charges from a priced source (invoice,
       provider billing export, or priced ledger), OR stamped
       explicitly UNVERIFIED. Runtime cost units alone do NOT certify
       economic execution (operator-loop-v1: abstract units until a
       priced ledger exists). A completed mission + cost-unit total
       is insufficient for the provider-expense field.
     — Pipeline value and collected cash attributed to this GE-001 run
       only. Any existing Annfiera payment predates this acceptance
       run and MUST stay separate from GE-001 attributed outcome.
 10. Durable evidence pack filed (mission, executions, approvals, side
     effects, economics, Console screenshots/records)
CONSTRAINTS:
  - No OTEL / Okta / Skills / Desks expansion
  - No client-facing Copilot until auth + durable sessions land (action 3)
  - Supervised named-human execution only
  - Do not count contaminated OL-001 toward Mission-001 gates
  - Do not attribute pre-existing Annfiera payment to GE-001 outcome
DEPENDENCIES:
  - RES-002 strategy (accepted)
  - Runtime #57 on main
  - Products #44 (merge gate)
  - Human VPS deploy + GHL cleanup credentials
EXPECTED OUTPUT:
  - Updated release manifest result: PASS (or explicit FAIL with blockers)
  - Live acceptance evidence pack
  - Incident cleanup append on runtime incident doc
TEST REQUIREMENTS:
  - Operator Loop v1 sequence + GOVERNED EXECUTION checklist
  - Deliberate restart at governed pause
  - Denial branch with zero side effect
  - Provider reconciliation before retry
SECURITY CONSIDERATIONS:
  - Synthetic→live boundary must be live-proven
  - Tenant + data-class declared on every production adapter path
  - Copilot remains internal-only until authenticated
STATUS:                  IN PROGRESS — Products #44 merged (06eedbf9, main CI green);
                         awaiting pinned Runtime #57 deploy + Annfiera cleanup + live GE-001
HANDOFF:                 See roadmap/governed-execution-certify.md ordered steps
```
