# Reliability audit — 2026-09-12

**Owner:** AION — QA (`.aion/agents/09-qa-engineer.md`)  
**Scope:** Baseline posture across canonical repos + `aion-desks`  
**Principle:** Code is not complete because it compiles. It is complete when
expected behavior is demonstrated.

## Executive verdict

Platform control plane (`aion-core` / `aion-data` / `aion-runtime`) has a
credible proof culture: unit/integration tests plus mission proof matrices and
restart/durability scenarios. Gaps concentrate in **UI surface gates**
(`aion-desks`), **explicit concurrency/race suites**, and **uneven unit-test
density** in Runtime vs proof scripts.

## Evidence run (this audit)

| Repo | Command | Result |
|---|---|---|
| `aion-core` | `npm ci && npm run check` (lint · typecheck · test · build) | **PASS** — 15 files / 90 tests; build OK |

Other repos were inventory-audited (scripts, CI, harness presence). Full
Postgres-backed Data/Runtime matrices were not re-executed in this agent VM
(no DB services provisioned for this charter pass). Treat those as
**CI-owned proof** until a follow-up acceptance run.

## Per-repo posture

### aion-core — PASS (strong)

| Gate | Present |
|---|---|
| lint / typecheck / test / build | Yes (`npm run check`) |
| CI | `.github/workflows/ci.yml` — lint · typecheck · test · build |
| Suites | contracts, policy, orchestration, integration lifecycle |

Notes: Phase-1 docs still mark concurrency control as out of scope for
in-memory adapters — correct for Core library; race proof belongs at Data /
Runtime boundaries.

### aion-data — PASS (strong, DB-dependent)

| Gate | Present |
|---|---|
| lint / typecheck / test / build | Yes |
| CI | Yes |
| Restart / durability | `tests/integration/durability-resume.test.ts` |
| Failure paths | denied-action, failed-execution |
| Optimistic concurrency | Repository layer (`version` / conflict errors) |

Vitest forced sequential (`sequence.concurrent: false`) to protect migration
rebuild races — intentional.

### aion-runtime — PASS (acceptance-heavy)

| Gate | Present |
|---|---|
| typecheck / build | Yes |
| CI verify | typecheck, portability, acceptance, M001–M009, IE002, platform-v01 cert |
| Image boot-certify | Yes (GATE A before publish) |
| Unit tests | Thin — primarily `outcomes-sessions-gateway.test.ts` |

Restart durability is proven via shell proof matrices (e.g. Mission 001 PASS D,
Mission 009 PASS G, CERT-DURABILITY), not only unit tests. That is acceptable
**if** CI stays green; QA must reject Runtime durability claims without a
matching proof script run.

### aion-products — PASS (partial for live paths)

| Gate | Present |
|---|---|
| typecheck / test | Yes |
| CI | Yes |
| Live CRM / GHL | Documented as gated; synthetic ≠ live proof |

Product RELEASE-STATUS already flags restart/concurrent update needs against
Postgres for durable Revenue storage — remain open until evidenced.

### aion-desks — FAIL (gates missing)

| Gate | Present |
|---|---|
| lint | `next lint` only |
| typecheck | **Missing** script |
| test | **Missing** |
| CI | **Missing** |

Blocker for any production claim that depends on desks checkout/Stripe flows
until typecheck + smoke/build CI exist.

### aion-docs — N/A (constitution)

No executable test suite expected. QA validates that claimed readiness docs
match proof artifacts.

### aion-infra — PARTIAL

Validate/deploy workflows exist. Not a substitute for application behavior
proof. Secrets/DR (Linear AIO-7) remain separate operational verification.

## Gap register

| ID | Severity | Gap | Recommended owner |
|---|---|---|---|
| QA-G1 | **Blocker** (for desks go-live) | `aion-desks` lacks typecheck, tests, CI | Frontend / Product + QA |
| QA-G2 | High | Few dedicated concurrency/race tests at Runtime/gateway submit path | Runtime + QA |
| QA-G3 | Medium | Runtime unit-test density low vs proof-script reliance | Runtime + QA |
| QA-G4 | Medium | No single cross-repo “acceptance checklist” automation beyond per-repo CI | QA / Orchestrator |
| QA-G5 | Medium | Live integration paths (GHL/CRM) still model/credential gated | Integrations + Product |
| QA-G6 | Low | Core Phase-1 concurrency explicitly deferred — track when multi-process Core appears | Architect / Runtime |

## Acceptance bar this role will enforce

A change or mission is **not done** if any of the following are true:

1. Touched repo has a `typecheck`/`test`/`build` script that was not run
2. Durability claimed without restart evidence (Data integration or Runtime proof)
3. Gate/deny/isolation claimed without a failing-path test or proof step
4. Handoff lists no commands/results
5. Failing tests were deleted or skipped to green CI

## Next QA actions

1. Add `aion-desks` `typecheck` + CI build/lint (minimum); smoke test for Stripe
   checkout path when credentials allow
2. Schedule Postgres-backed `aion-data` + `aion-runtime` proof re-run in CI or
   a provisioned agent environment; attach logs to go-live missions
3. Add at least one explicit concurrent-submit / duplicate-side-effect test at
   Runtime or Data boundary for idempotent resume
4. Review every specialist PR handoff against this audit’s gap register

## Related canon

- [engineering/testing.md](../../engineering/testing.md)
- [engineering/definition-of-done.md](../../engineering/definition-of-done.md)
- [engineering/production-readiness.md](../../engineering/production-readiness.md)
- [.aion/standards/testing.md](../standards/testing.md)
- [.aion/agents/09-qa-engineer.md](../agents/09-qa-engineer.md)
