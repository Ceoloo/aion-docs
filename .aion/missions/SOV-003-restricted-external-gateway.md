# Agent mission — SOV-003 Restricted External Gateway IP policy (P0)

```markdown
MISSION ID:              SOV-003
TITLE:                   Restricted External Gateway — default-deny outbound IP policy on Runtime
BUSINESS OUTCOME:        No unauthorized IP egress; every external call is allowlisted, classified, and audited
PRIMARY OWNER:           Security Engineer (08)
SUPPORTING AGENTS:       Runtime (01), Integrations (07), Core/Architect (10), Data (02), AI (03)
REVIEWERS:               Architect, Security, QA
REPOSITORY:              aion-docs (contract) → aion-core (types) → aion-runtime (enforce) → adapters
BRANCH / WORKTREE:       cursor/sovereign-intelligence-doctrine-ff59 (docs); implement branches per repo
CONTEXT:                 SOV-001 P0; ADR-016 REG requirement; ADR-003 Execution Gateway; SEL ADR-012; GHL live; products frontier side-door
OBJECTIVE:               Bind REG policy; Core OutboundPolicy types; Runtime evaluate-before-adapter; v1 allowlist (GHL/Stripe/B2/ntfy/public_frontier); observe→enforce rollout
ACCEPTANCE CRITERIA:
  - ADR-018 Accepted + architecture/restricted-external-gateway.md published
  - IpExposureClass + OutboundPolicyRequest/Decision specified (Core-ready)
  - Default deny documented; v1 allowlist published
  - Runtime path: evaluate() before GHL (and other) adapter fetch — observe mode first, then enforce
  - Deny and allow decisions audited (execution/event/side-effect spine)
  - Products proprietary→frontier side-door closed or blocked in concert with SOV-002
  - Feature flag reg.enforce with documented rollout
  - No production untested cutover of GHL SoE
CONSTRAINTS:
  - REG is policy on existing Runtime gateway — no second microservice
  - GHL remains authorized SoE (not intelligence SoR)
  - Desks Stripe may stay edge-allowlisted until Runtime mediation ADR
  - Do not put secrets in audit payloads
DEPENDENCIES:            SOV-001 done; ADR-016; SOV-002 for inference classification alignment
EXPECTED OUTPUT:         ADR-018 + REG architecture; Core policy types PR; Runtime enforcement PR; allowlist config; observe/enforce evidence
TEST REQUIREMENTS:       Unit tests for deny proprietary/unknown; allow soe_authorized GHL fixture; CI guard against raw provider hosts outside adapter modules (stretch)
SECURITY CONSIDERATIONS: Fail closed when enforce on; observe mode must not be permanent; exception ADRs time-bounded
STATUS:                  contracted (docs/ADR landed 2026-10-20); implementation not started
HANDOFF:                 see below
```

## Contracts

- [ADR-018: Restricted External Gateway](../../adr/ADR-018-restricted-external-gateway.md)
- [architecture/restricted-external-gateway.md](../../architecture/restricted-external-gateway.md)
- [ADR-017 model router](../../adr/ADR-017-sovereign-model-router.md) (inference class → IP class)
- [ADR-003](../../adr/ADR-003-execution-gateway-into-runtime.md), [SEL v1](../../architecture/secure-execution-layer-v1.md)
- Core existing: `ExternalSideEffect` (`aion-core` contracts)

## Implementation slices (ordered)

| # | Slice | Repo | Owner |
|---|---|---|---|
| 0 | Docs/ADR (this mission) | `aion-docs` | Architect + Security |
| 1 | Core `IpExposureClass` + `OutboundPolicy*` | `aion-core` | Security + Core |
| 2 | Runtime `OutboundPolicy.evaluate` + `reg.enforce` flag | `aion-runtime` | Runtime + Security |
| 3 | GHL adapter: classify `soe_authorized`; call evaluate before fetch | `aion-runtime` | Integrations |
| 4 | Audit record on allow/deny | `aion-data` / runtime events | Data + Runtime |
| 5 | Observe → enforce on staging → production | Runtime + Security | Security |
| 6 | Coordinate products frontier closure with SOV-002 | products | AI + Security |

## Handoff (docs slice)

```text
MISSION                  SOV-003
STATUS                   contracted — implementation pending
SUMMARY                  ADR-018 Accepted; REG architecture published. Default deny; SoE allowlist for GHL/Stripe; public_frontier only with none + non_proprietary. Runtime enforce + Core types remain.
FINDINGS                 Products→frontier is the largest side-door; GHL path needs evaluate-before-fetch without shutting CRM SoE.
IMPLEMENTATION           Docs/ADR only in this slice
FILES CHANGED            adr/ADR-018-*, architecture/restricted-external-gateway.md, this mission
NEXT RECOMMENDED ACTION  Security/Core open aion-core policy types PR; Runtime wire observe-mode evaluate around GHL adapter
```
