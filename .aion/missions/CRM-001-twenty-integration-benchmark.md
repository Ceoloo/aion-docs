# Agent mission — CRM-001 Twenty Integration & Benchmark

```markdown
MISSION ID:              CRM-001
TITLE:                   Twenty Integration & Benchmark (AION CRM Sovereign Infrastructure V1)
BUSINESS OUTCOME:        AION can run governed lead→opportunity workflows against a self-hosted Twenty CRM SoE, with EspoCRM benchmark data, without agents touching CRM databases — path opened for staged GHL migration
PRIMARY OWNER:           Integrations Engineer (07)
SUPPORTING AGENTS:       Runtime (01), Data (02), Infrastructure (06), Security (08), Architect (10), QA (09)
REVIEWERS:               Architect, Security, QA
REPOSITORY:              aion-docs (contract) → aion-infra (Twenty/Espo compose) → aion-runtime (adapter) → aion-core (catalog touch-ups) → aion-data (ledger provider only until SoR ADR)
BRANCH / WORKTREE:       cursor/crm-soe-ownership-adr-15f7 (docs); implement branches per repo
CONTEXT:                 Leadership direction 2026-10-20 — Twenty primary SoE; EspoCRM benchmark; AION Data SoT for canonical customer/revenue (boundary TBD); Runtime execution; AION OS workspace. ADR-019 Accepted. Reuse audit: .aion/audits/crm-001-infrastructure-reuse-2026-10-20.md
OBJECTIVE:               Reuse existing crm.* plane; deploy Twenty (dev, private net); benchmark EspoCRM; ship Twenty CrmBackend; prove first workflow; prepare staged GHL migration — create no new microservices
ACCEPTANCE CRITERIA:
  - Reuse audit published and reviewed (no unjustified new services)
  - Twenty running in dev with private networking + isolated credentials (separate DB from aion_data)
  - EspoCRM benchmark report: memory, CPU, API latency sample, deploy step count vs Twenty
  - Provider-neutral CrmBackend extraction (or equivalent) + Twenty fake backend green on RCAOS-class proofs
  - Live Twenty backend: contact + company mapping strategy documented; opportunity + task/note (activity) through Runtime
  - Agents/products have zero Twenty DB URLs / direct API keys — Runtime only
  - First workflow verified: lead capture → qualification → opportunity → human-approved action → Outcome record
  - GHL remains available as migration adapter; rollback path documented (no forced prod cutover in this mission)
  - REG/SOV-003: Twenty classified (prefer internal/same-host); public egress still default-deny
CONSTRAINTS:
  - Do not create a new aion-crm repo or microservice
  - Do not add customer entity tables to aion-data until a follow-up data-layer ADR amends the “no CRM tables” rule
  - Do not cut over production GHL tenants in CRM-001
  - Do not give agents direct CRM database access
  - EspoCRM is benchmark-only unless a later ADR promotes it
DEPENDENCIES:            ADR-019 Accepted; reuse audit; SOV-003 observe path preferred before public SoE sprawl; GE-001/RCAOS process contracts remain reference workflows
EXPECTED OUTPUT:         Docs (ADR+audit+mission); infra compose overlay; runtime Twenty adapter PR; benchmark markdown; workflow proof evidence
TEST REQUIREMENTS:       fake Twenty proofs mirroring proof:rcaos-at / aio17 matrix; live Twenty against disposable workspace; no writes to production GHL during Twenty proofs
SECURITY CONSIDERATIONS: Isolated creds; internal Docker network; PIT/API keys never in VITE_*; tenant isolation tests; PII stays on AION-controlled hosts
STATUS:                  defined — docs/audit slice in progress
HANDOFF:                 see below
```

## Target architecture (V1)

| Component | Decision | Role |
|---|---|---|
| Primary CRM | **Twenty CRM** | API-extensible operational interface (SoE) |
| Benchmark | **EspoCRM** | Lightweight performance / deploy comparison |
| Source of truth | **AION Data** | Canonical customer & revenue records *(schema amendment required — see audit)* |
| Intelligence | **AION AI Layer** | Locally controlled models and agents |
| Execution | **AION Runtime** | Governed actions, permissions, audit |
| Workspace | **AION OS** | Unified operator and client experience |

## Non-negotiables

1. **Sovereign ownership** — AION controls proprietary customer data, intelligence, and business logic.
2. **Vendor independence** — Twenty is swappable via `crm.*` + `CrmBackend` without rebuilding Core.
3. **Offline resilience** — Core records, local intelligence, and internal workflows function without public services; external communications resume when connectivity returns.

## Implementation slices (ordered)

| # | Slice | Repo | Owner |
|---|---|---|---|
| 0 | ADR-019 Accepted + reuse audit + this mission | `aion-docs` | Architect + Integrations |
| 1 | Twenty (and Espo) compose overlay — internal network, own DB, secrets | `aion-infra` | Infrastructure |
| 2 | EspoCRM benchmark script + report | `aion-infra` / docs | Infrastructure + QA |
| 3 | Extract `CrmBackend` port (`adapters/crm/`); keep `adapters/ghl/` leaf; add fake Twenty; clean soft `provider: 'ghl'` leaks | `aion-runtime` | Integrations + Runtime |
| 4 | Live Twenty backend + map onto existing `CrmContact` / `CrmOpportunity` normalize shapes | `aion-runtime` | Integrations |
| 5 | Catalog: company decision (`crm.company.*` vs enrich); un-pin `metadata.provider: 'ghl'`; optional `crm_connection` vs `ghl_connection` | `aion-core` | Core + Integrations |
| 6 | Ledger explicit `provider=twenty` (no default `ghl`) + dual-run proofs | `aion-runtime` + `aion-data` | Runtime + Data |
| 7 | First workflow proof (fake → live Twenty) | Runtime + QA | QA |
| 8 | Staged GHL migration plan (reconcile, isolation, rollback) — docs + dry-run | Integrations | Integrations + Security |
| 9 | *(Follow-up ADR)* Canonical customer SoR in aion-data | `aion-docs` → `aion-data` | Architect + Data |

## Contracts

- [ADR-019: CRM SoE ownership path](../../adr/ADR-019-crm-soe-ownership-path.md)
- [Reuse audit 2026-10-20](../audits/crm-001-infrastructure-reuse-2026-10-20.md)
- [ADR-016](../../adr/ADR-016-sovereign-intelligence-doctrine.md), [ADR-018](../../adr/ADR-018-restricted-external-gateway.md)
- [ghl-capabilities.md](../../offers/revenue-capture-appointment-os/ghl-capabilities.md) (scorecard until generalized)
- Runtime adapter reference: `aion-runtime/src/adapters/ghl/`

## Handoff (docs / audit slice)

```text
MISSION                  CRM-001
STATUS                   defined — docs slice ready for implement branches
SUMMARY                  Official V1 stack locked (Twenty SoE, Espo benchmark, Data SoT TBD schema, Runtime execution). Reuse audit: no new CRM microservice; extract CrmBackend; Twenty compose on internal net.
FINDINGS                 aion-data currently forbids CRM tables — blocks literal “customer SoT in Data” until follow-up ADR. GHL plane is the reusable substrate.
IMPLEMENTATION           Docs/ADR/audit/mission only in this slice
FILES CHANGED            adr/ADR-019-*, .aion/audits/crm-001-*, .aion/missions/CRM-001-*, adr/README, missions README
NEXT RECOMMENDED ACTION  Infra: Twenty compose overlay on internal network; Runtime: CrmBackend extraction spike with fake Twenty
```
