# AION Engineering Team — Responsibility Matrix

## Roles

| ID | Role | Chat name | Primary repos | Mode |
|---|---|---|---|---|
| 00 | Orchestrator | `AION — ORCHESTRATOR` | all (routing only) | Permanent |
| 01 | Runtime Engineer | `AION — RUNTIME` | `aion-runtime`, parts of `aion-core` execution | Permanent |
| 02 | Data Engineer | `AION — DATA` | `aion-data` | Permanent |
| 03 | AI / Agent Engineer | `AION — AI` | `aion-core` intelligence/agent surfaces, product agent logic | On-demand (often) — deterministic-first; agents only when measurable value |
| 04 | Product Engineer | `AION — PRODUCT` | `aion-products`, `aion-desks` | Permanent |
| 05 | Frontend / UX | `AION — FRONTEND` | `aion-products/web`, `workforce-control`, `aion-desks` | Permanent |
| 06 | Infrastructure | `AION — INFRA` | `aion-infra`, deploy configs | Permanent |
| 07 | Integrations | `AION — INTEGRATIONS` | adapters (GHL, Stripe, Notion, …) | On-demand |
| 08 | Security | `AION — SECURITY` | policy, permissions, secrets boundaries | On-demand |
| 09 | QA / Reliability | `AION — QA` | tests across repos | Permanent — proof over compile; reviews other agents’ handoffs |
| 10 | Architect / Reviewer | `AION — ARCHITECT` | `aion-docs` ADRs + cross-repo review | Permanent |
| 11 | Documentation | `AION — DOCS` | `aion-docs`, READMEs | On-demand |
| 12 | Research / Prototype | `AION — RESEARCH` | spikes / PoCs (isolated) | On-demand |

---

## Repository ownership map

| Repository | Primary owner | Assist | Must not own |
|---|---|---|---|
| **aion-docs** | Architect, Docs | Orchestrator | production app code |
| **aion-core** | Runtime (execution contracts), AI (agent/intel), Security (policy) | Architect | DB schemas, UI, provider IaC |
| **aion-data** | Data | Runtime, Security | orchestration policy, product UI |
| **aion-runtime** | Runtime | Infra, Data, Security | product logic, provider SDKs |
| **aion-infra** | Infra | Security, QA | business logic, canonical schemas |
| **aion-products** | Product | Frontend, AI, Integrations | platform primitives, infra |
| **aion-desks** | Product + Frontend | Integrations (Stripe) | control-plane / canonical data |

Dependency direction (binding): see
[`../repositories/dependency-rules.md`](../repositories/dependency-rules.md).

```
aion-products ──▶ aion-core ──▶ aion-data
aion-runtime  ──▶ aion-core + aion-data
aion-infra    ──▶ deploys aion-runtime image (no code import cycle)
aion-docs     ──▶ governs all (no code dependency)
```

---

## Chat naming convention

Use exact names so context stays attached to a department:

```
AION — ORCHESTRATOR
AION — RUNTIME
AION — DATA
AION — AI
AION — PRODUCT
AION — FRONTEND
AION — INFRA
AION — INTEGRATIONS
AION — SECURITY
AION — QA
AION — ARCHITECT
AION — DOCS
AION — RESEARCH
```

Optional mission-scoped chats (temporary):

```
AION — MISSION/<id> — <short title>
```

Close mission chats after handoff; keep department chats.

---

## Keep permanently open

These preserve context and make daily delegation fast:

1. **AION — ORCHESTRATOR** — intake, routing, merge order
2. **AION — RUNTIME** — execution platform
3. **AION — DATA** — schemas / durability
4. **AION — PRODUCT** — Revenue Copilot / product missions
5. **AION — FRONTEND** — consoles and operator UX
6. **AION — INFRA** — deploy / environments
7. **AION — QA** — proof and regression
8. **AION — ARCHITECT** — boundary protection

---

## Open only when needed

- **AION — AI** — prompts, multi-agent patterns, evals, model routing
- **AION — INTEGRATIONS** — GHL, Stripe, Notion, OAuth, webhooks
- **AION — SECURITY** — permissions, approval gates, tenant isolation, secrets
- **AION — DOCS** — runbooks, ADRs, README sync after major changes
- **AION — RESEARCH** — vendor/library spikes before production commitment

---

## Parallel-work guidelines

**Encourage parallel work** when contracts are agreed and file ownership does not collide.

Example parallel slice for durable execution:

| Agent | Work |
|---|---|
| Runtime | recovery / restart path |
| Data | `ExecutionRecord` schema + migration |
| Frontend | operator execution view |
| Infra | health checks / deploy wiring |
| QA | restart + failure harness |

**Rules**

1. Define the **contract** (fields, statuses, API) before parallel coding.
2. One primary writer per file set / package. Use separate branches or worktrees.
3. Do not parallel-edit the same migration, gateway route, or shared contract file.
4. Orchestrator sets merge order: Data contracts → Runtime → Product/Frontend → Infra → QA proof.
5. If two agents need the same file, serialize or split ownership with an ADR.

---

## Company priority filter

Prefer work that improves:

1. revenue outcomes
2. real workflow execution
3. reliability / restart survival
4. observability / auditability
5. reuse across products
6. client deployment speed

Reject / defer: decorative dashboards, speculative infra, second abstractions for
already-owned capabilities.
