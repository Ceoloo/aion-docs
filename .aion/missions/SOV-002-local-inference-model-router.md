# Agent mission — SOV-002 Local inference + sovereign model router (P0)

```markdown
MISSION ID:              SOV-002
TITLE:                   Local inference path + Sovereign Model Router (proprietary fail-closed)
BUSINESS OUTCOME:        Proprietary Copilot/Revenue inference no longer depends on public frontier APIs; model swappable without rebuilding workflows
PRIMARY OWNER:           AI / Agent Engineer (03)
SUPPORTING AGENTS:       Infrastructure (06), Runtime (01), Product (04), Security (08), Architect (10)
REVIEWERS:               Architect, Security, QA
REPOSITORY:              aion-docs (contract) → aion-core (types) → aion-infra (local host) → aion-products (cutover)
BRANCH / WORKTREE:       cursor/sovereign-intelligence-doctrine-ff59 (docs); implement branches per repo
CONTEXT:                 SOV-001 P0; ADR-016 doctrine; RES-001 conditional GO for Core types; products today call Anthropic/OpenRouter with live context
OBJECTIVE:               Land binding router policy; Core model.generate@1 types; working local inference for proprietary class; products default proprietary→local with no silent frontier fallback
ACCEPTANCE CRITERIA:
  - ADR-017 Accepted + architecture/sovereign-model-router.md published
  - Inference classes proprietary | non_proprietary | unknown defined and used in router contract
  - Core types for model.generate@1 + RouteDecision (no vendor SDKs in Core/Runtime)
  - Local inference host reachable from staging (technology choice recorded)
  - Products proprietary path uses local (or fail-closed); public frontier only for non_proprietary via REG
  - Keyless CI still green; eval pack exists for local quality on scrubbed fixtures
  - No silent OpenRouter/Anthropic fallback when local fails
CONSTRAINTS:
  - No provider SDKs in aion-core or aion-runtime
  - Do not untested-shutdown revenue paths — use time-bounded exception ADR if cutover blocked
  - Adaptive multi-provider auto-routing remains non-goal (RES-001)
  - Mission-before-infra: prove one local path before multi-region GPU sprawl
DEPENDENCIES:            SOV-001 done; ADR-016; SOV-003 REG for any remaining public inference allow
EXPECTED OUTPUT:         ADR-017 + router architecture; Core PR; infra local-runtime runbook; products router cutover PR; eval evidence
TEST REQUIREMENTS:       Unit tests for classify/route; keyless provider fakes; staging smoke on local model; eval non-regression notes
SECURITY CONSIDERATIONS: Proprietary prompts never leave boundary; redact secrets in errors; local host auth/network hardened
STATUS:                  contracted (docs/ADR landed 2026-10-20); implementation not started
HANDOFF:                 see below
```

## Contracts

- [ADR-017: Sovereign Model Router](../../adr/ADR-017-sovereign-model-router.md)
- [architecture/sovereign-model-router.md](../../architecture/sovereign-model-router.md)
- [ADR-018 / REG](../../adr/ADR-018-restricted-external-gateway.md) (public inference gate)
- Prior: [RES-001 architect decision](../research/RES-001-architect-decision.md)

## Implementation slices (ordered)

| # | Slice | Repo | Owner |
|---|---|---|---|
| 0 | Docs/ADR (this mission) | `aion-docs` | Architect |
| 1 | Core `model.generate@1` + `InferenceClass` + `ModelRouteDecision` | `aion-core` | AI Engineer |
| 2 | Local inference host + runbook (choose ollama/vLLM/llama.cpp in implement note) | `aion-infra` | Infra + AI |
| 3 | Products: classify requests; route proprietary→local; deny silent frontier | `aion-products` | AI + Product |
| 4 | Staging smoke + scrubbed eval pack | products + docs | AI + QA |
| 5 | Remove or REG-gate remaining public proprietary calls | products | AI + Security |

## Handoff (docs slice)

```text
MISSION                  SOV-002
STATUS                   contracted — implementation pending
SUMMARY                  ADR-017 Accepted; sovereign-model-router architecture published. Proprietary→local-only; unknown fail-closed; no silent frontier fallback. Core types + local host + products cutover remain.
FINDINGS                 Products Anthropic/OpenRouter path is the primary IP egress to replace (SOV-001 S-05/S-06).
IMPLEMENTATION           Docs/ADR only in this slice
FILES CHANGED            adr/ADR-017-*, architecture/sovereign-model-router.md, this mission
NEXT RECOMMENDED ACTION  AI Engineer opens aion-core types PR; Infra spikes local runtime on VPS
```
