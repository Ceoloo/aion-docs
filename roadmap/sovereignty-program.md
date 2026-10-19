# Sovereignty Program

> Implements [ADR-016](../adr/ADR-016-sovereign-intelligence-doctrine.md) and the
> [Sovereign Intelligence Doctrine](../architecture/sovereign-intelligence-doctrine.md).

This program is the ordered path from today’s hosted dependencies to an
AION-controlled intelligence infrastructure. It is **mission-driven**: each
phase opens concrete missions; it is not speculative infrastructure for its own
sake.

---

## Phase 1 — Sovereignty audit (P0)

**Goal:** Know every exposure path before changing production.

Inventory and classify:

| Inventory item | Classification needed |
|---|---|
| Providers (cloud, SaaS, inference, auth, analytics) | Critical vs optional; proprietary data touch? |
| Outbound APIs and webhooks | Default-deny candidate vs authorized external |
| Data dependencies | Authoritative copy location; key custody |
| Model dependencies | Mandatory vs replaceable; proprietary prompts/context? |
| Credentials and secrets | Who holds keys; blast radius |
| Possible IP exposures | Source, architecture, customer data, operational memory |

**Exit criteria:**

- [x] Written inventory across `aion-core`, `aion-data`, `aion-runtime`,
  `aion-infra`, `aion-products`, `aion-desks`, and AI tooling paths.
  → [../.aion/audits/sovereignty-inventory-2026-10-19.md](../.aion/audits/sovereignty-inventory-2026-10-19.md)
- [x] Each item labeled: **compliant**, **migrate**, **optional-external**, or
  **exception-ADR-required**.
- [x] Priority ordered migration backlog for Phase 2.
  → same audit §6 (SOV-002…SOV-006 suggested).

**Status:** Phase 1 audit complete 2026-10-19 (SOV-001). Awaiting
Architect / Security / CEO review before Phase 2 execution.

**Owners:** Architect (lead), Security Engineer, Infrastructure Engineer,
Integrations Engineer.

---

## Phase 2 — Independent core

**Goal:** AION-controlled operating paths for critical capability.

Deploy / prove:

- Self-hosted (or AION-controlled) identity
- Persistence under AION encryption keys
- Local / self-hosted inference path for proprietary tasks
- Execution queues with durable recovery
- Secrets management AION controls
- Audit logging that remains available offline from public SaaS

**Exit criteria:**

- [ ] Critical data plane has an AION-controlled path (Supabase migration
  underway or complete for protected stores).
- [ ] Proprietary inference does not require public frontier APIs.
- [ ] Secrets and audit trails recoverable without a single vendor login.
- [ ] Documented deploy + restore procedures exist and have been dry-run.

**Owners:** Infrastructure Engineer, Data Engineer, Runtime Engineer,
AI / Agent Engineer, Security Engineer.

---

## Phase 3 — Offline operation

**Goal:** Prove core continuity with internet disconnected.

Test with network isolation:

- Critical internal missions
- Agent inference (local)
- Retrieval / RAG against owned stores
- Approvals and policy enforcement
- Queued external communications until connectivity returns

**Exit criteria:**

- [ ] Written offline test protocol and results.
- [ ] Critical internal missions complete without internet.
- [ ] External outbound work queues and drains correctly on reconnect.
- [ ] Failures are observable via local audit trails.

**Owners:** Runtime Engineer, QA / Reliability, Security Engineer.

---

## Phase 4 — Sovereign intelligence optimization

**Goal:** Improve owned intelligence without reintroducing mandatory rent.

- Locally deployable specialized models where justified
- Permissioned knowledge retrieval
- Independent evaluations
- Fine-tuning only when economically justified and keys/data stay AION-owned

**Exit criteria:**

- [ ] Model router prefers local / owned paths for proprietary tasks by default.
- [ ] Eval suite runs without public API dependency for core scenarios.
- [ ] Any fine-tune artifacts and datasets remain under AION control.

**Owners:** AI / Agent Engineer, Research / Prototype Engineer, Architect.

---

## Phase 5 — Portable customer infrastructure

**Goal:** Package AION for customer-controlled environments.

- Customer-controlled servers
- Private clouds
- On-premises deployments

**Exit criteria:**

- [ ] Documented packaging and deployment for at least one customer-controlled
  topology.
- [ ] Portable data formats and restore verified.
- [ ] Same acceptance gate applies to customer deployments.

**Owners:** Infrastructure Engineer, Product Engineer, Documentation Engineer.

---

## Stack migration directions (from doctrine)

| Component | Direction | Phase emphasis |
|---|---|---|
| Supabase | Self-hosted / AION-controlled persistence for critical data | 1–2 |
| Vercel | Temporary delivery OK; self-hosted deploy path required | 2–5 |
| Frontier model public APIs | Prohibited for proprietary tasks; local inference for core | 1–2 |
| AION Runtime | Offline queues, policy, local recovery | 2–3 |
| Agent memory / RAG | Owned storage + retrieval | 2–4 |
| GHL / third-party CRM | Authorized external; not SoR for protected intelligence | 1–2 |
| AI development tools | Restrict protected repos, architecture, confidential context | 1 ongoing |

---

## Working rules

1. **Audit before cutover.** No untested shutdown of a critical path.
2. **Classify exceptions.** Time-bounded exception ADRs only; never silent
   permanent bypasses.
3. **Acceptance gate on every critical proposal.** See doctrine §5.
4. **Mission before infrastructure.** Each phase opens missions with owners and
   exit criteria; do not build speculative multi-region sovereignty theater.
