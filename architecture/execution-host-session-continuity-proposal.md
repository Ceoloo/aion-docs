# AION Execution Host & Session Continuity — Cloudroom Reference Proposal

**Status: PROPOSED — documentation only, not production authorization.** Prepared October 15, 2026. Tracking: [Linear P-AIO-8](https://linear.app/aion-empire/project/aion-execution-host-and-session-continuity-abbe7168ebe6) and [Notion architecture brief](https://app.notion.com/p/3fb5b5fa4f8f81aab033d3d8cdd5110c?pvs=204).

## Decision framing
Use Cloudroom Core as a **reference implementation** for native agent process management, journaled request receipts, recoverability, bounded Linux workloads and streaming. AION already accepted [ADR-011](../adr/ADR-011-harness-execution-seam.md): UHP/HarnessRouter is the intended replaceable **execution backend** behind AION Runtime and its vendor-neutral HarnessExecutionProvider port. Cloudroom native API compatibility with UHP is unverified; compare, do not assume compatibility or rewrite Runtime.

AION Runtime remains the **sole authority for authentication, tenant identity, policy, human approval, business side effects, outcomes, and governance audit**. A proposed execution host receives only a policy-approved unit of work and emits facts. No agent receives long-lived line-of-business credentials or broader shell/network permissions than authorized.

## Reference code (reviewed)
| Cloudroom source | Study for |
| --- | --- |
| [Session manager](https://github.com/davidondrej/cloudroom-core/blob/main/src/session/mod.rs) | Native lifecycle and durable requests |
| [Journal/outbox](https://github.com/davidondrej/cloudroom-core/blob/main/src/session/outbox.rs) | Append-only records, replay and acknowledged uploads |
| [Process driver](https://github.com/davidondrej/cloudroom-core/blob/main/src/runtime/process.rs) | Bounded stdio and graceful/forced shutdown |
| [Linux containment](https://github.com/davidondrej/cloudroom-core/blob/main/src/runtime/linux.rs) | Workload groups and descendant cleanup |
| [Teleport](https://github.com/davidondrej/cloudroom-core/blob/main/src/session/teleport.rs) | Staged transfer (deferred AION feature) |
| [Lifecycle contract](https://github.com/davidondrej/cloudroom-core/blob/main/docs/session-lifecycle.md) | Restart and unknown-effect semantics |
| [Cloudroom GUI](https://github.com/davidondrej/cloudroom-gui) | Desktop UX only, not authorization design |

## Architectural boundaries
1. AION Workspace / Command Center: present mission, session, artifact, health, review, approval. Browser has no Runtime secret or actor-setting authority.
2. AION Runtime + Core: authenticate, tenant-bind, risk-classify, approve and dispatch through HarnessExecutionProvider, audit external effects.
3. **Proposed isolated execution host:** native CLI launch, bounded queue and receipt, journal, native history resume preflight, process cleanup and streams. It cannot create new policy.
4. AION Data: canonical governance records (mission, execution, approval, side effect, outcome) with correlation IDs. Product files/transcripts follow their owning module stores.
5. AION Infra: secret resolver, deny-default egress, sandbox isolation, resource monitoring, kill switch and rollback.

## Invariants for proof
- Receipt durably accepted before responding; stable request/idempotency key survives retries. Concurrent duplicates do not repeat dispatch. Same key/different payload is rejected.
- Interrupted **dispatched** work becomes unknown/indeterminate, not a definite failure or safe retry. Require reconciliation before re-executing an external mutation. No blanket exactly-once claim.
- Missing/corrupt native history cannot silently start a fresh context. Unsafe disk state stops mutation readiness; history remains available for investigation.
- Runtime-approved actor/tenant and scopes are immutable at the host boundary; no raw business secret or arbitrary egress in harness shell.
- Parent/child sessions may only attenuate authority. Revoke blocks future work and contains active children where host isolation allows.
- Every run has queryable correlation between mission, execution, session, harness, native turn, artifacts, cost/usage and observed outcome.
- No alternate execution gateway, direct agent-to-CRM writes, unattended prod writes, auto-publish, local Mac Full access, or live client launch.

## Work packages
| ID | Outcome | Gate |
| --- | --- | --- |
| EH-001 | Evidence-backed Cloudroom vs existing UHP/ADR-011 decision matrix | Research only |
| EH-002 | Versioned host/session contract and SEL threat model | Design review |
| EH-003 | Authorized harmless Git edit in disposable sandbox | GE-001 accepted; EH-001/002 reviewed |
| EH-004 | Crash/receipt/unknown-state recovery proof | EH-003 |
| EH-005 | Tenant, egress, secrets, revoke and workload kill assurance | EH-002/003 |
| EH-006 | Runtime-backed Agent Room/session display inside Workspace | EH-003/004, existing OS dependencies |
| EH-007 | Staging test matrix, rollout runbook, rollback and authorized maintainer release gate | GE-001, EH assurance and human go/no-go |

## Acceptance criteria for an eventual canary
- Real authorized principal and tenant; PolicyEngine authorization and, where required, approval, with execution event evidence.
- Safe duplicate/no-duplicate replay behavior across intentional crashes; meaningful indeterminate/reconcile state.
- Tested isolation against cross-tenant access, secrets exfiltration and network/FS escape.
- Descendant processes stopped on revocation, accurate health/readiness and observable resource limits.
- Real harness evidence distinguished from inference-free fixtures, documented threat review and patch/version pinning.
- Staging dry run and rollback digest; explicit maintainer approval; post-deploy serving digest and health verified. **CI green alone is not production acceptance.**

## Dependencies and non-goals
**GE-001 certification and Pilot-to-Revenue are P0.** Existing operator path [AIO-35](https://linear.app/aion-empire/issue/AIO-35/p0-maintainer-deploy-runtime-57-and-capture-production-identity) → [AIO-36](https://linear.app/aion-empire/issue/AIO-36/p0-authorized-ghl-cleanup-of-identified-annfiera-synthetic-note) → [AIO-37](https://linear.app/aion-empire/issue/AIO-37/p0-certify-ge-001-on-a-fresh-low-risk-real-record) stays ahead of host implementation. Do not claim production readiness from previous status summaries. No timeline or deployed work authorized by this proposal.

See [ADR-012](../adr/ADR-012-secure-execution-layer-v1.md), [ADR-015 (proposed)](../adr/ADR-015-workspace-product-module-contract.md), [Cloudroom Core](https://github.com/davidondrej/cloudroom-core), [Cloudroom GUI](https://github.com/davidondrej/cloudroom-gui), and [Cloudroom download](https://www.cloudroom.dev/download).
