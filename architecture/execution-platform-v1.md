# AION Execution Platform v1 — Architecture Specification

- **Status:** Active build contract
- **Date:** 2026-09-06
- **Audience:** Grok / Cursor / human engineering agents implementing Week 1–4
- **Supersedes (conceptually):** “second Grok workspace = all AION companies”
- **Companion:** [ADR-003](../adr/ADR-003-execution-gateway-into-runtime.md),
  Notion Progress Assessment (Sep 2026), Mission 001 / 002

## 0. The one sentence

**AION Holding decides WHAT. The Execution Platform determines HOW. Ventures
produce the RESULT. Models (Grok, Kimi, Claude, …) are interchangeable
intelligence suppliers — not the operating system.**

The second Grok environment is **not** “the place where all AION companies
live.” It is an **operator surface onto AION’s shared Execution Plane**, with
isolated domains, identities, permissions, telemetry, and golden paths.

---

## 1. Four planes (empire model)

```text
                         AION FAMILY
                             │
                             ▼
                    ┌─────────────────┐
                    │  AION HOLDING   │  STRATEGY PLANE
                    │ WHAT / WHY /    │  capital · governance · priority
                    │ CAPITAL         │
                    └────────┬────────┘
                             │
                             ▼
              ┌───────────────────────────┐
              │ AION EXECUTION PLATFORM   │  EXECUTION PLANE
              │ HOW WORK GETS DONE        │
              │ Runtime · Agents · Skills │
              │ Catalog · Permissions     │
              │ Gates · Evals · Cost/ROI  │
              └────────────┬──────────────┘
                           │
       ┌───────────┬───────┼────────┬────────────┐
       ▼           ▼       ▼        ▼            ▼
    Systems      Media   G-Star   Assets      Frontier
                                              (domain / economic engines)
                           │
                           ▼
              ┌───────────────────────────┐
              │ DATA + KNOWLEDGE PLANE    │  institutional memory
              └───────────────────────────┘
```

| Plane | Owns | Does **not** own |
|---|---|---|
| **Strategy (Holding)** | Capital, portfolio doctrine, kill/grow, risk, allocation | Day-to-day tool calls, deploys, CRM writes |
| **Execution** | Agent identity, service catalog, workflows, Runtime ingress, autonomy, cost, evals | Venture P&L strategy |
| **Domain / venture** | Outcomes, domain data, customer/IP relationships | Parallel control planes |
| **Data + Knowledge** | Canonical records, scoped knowledge, audit, lessons | Model choice as architecture |

**Doctrine:** Holding decides WHAT. Execution Platform determines HOW. Ventures
produce the RESULT.

---

## 2. Capability domains (not 12 companies)

Do **not** spin autonomous agent orgs for every industry idea yet. Start with
five capability domains:

| Domain | Role | Contains (examples) |
|---|---|---|
| **AION Systems** | Technology engine (P0) | Software, AI/automation, platform, cloud, network, security, TechOps, data infra, agent systems |
| **AION Media** | Attention / distribution | Content, marketing, sales enablement, production; Digital Empire as brand under Media when ready |
| **G-Star Empire Records** | Separate economics | Label IP, talent, royalties — **consumes** Systems/Media/Execution, does not rebuild them |
| **AION Assets** | Capital-intensive physical | Real estate, fleet, DC property — later |
| **AION Frontier** | R&D / incubation | Robotics, mech, blockchain/crypto infra, experimental compute — **low capital** until earned |

**Allocation (until Mission 001 + 002 pass):**

- **70%** Systems / revenue-producing execution
- **20%** reusable Execution Platform infrastructure
- **10%** G-Star / Media / Frontier experiments

**Proof domains for the platform abstraction:** Systems Revenue **and**
G-Star/Media. Only then Assets / Frontier.

---

## 3. TechOps → AION Systems

TechOps is **not** a standalone business. It is a capability under Systems:

```text
AION SYSTEMS
├── AI & Automation
├── Software Engineering
├── Platform Engineering
├── Cloud Engineering
├── Network Engineering
├── Cybersecurity
├── Data Engineering
├── Infrastructure / TechOps
└── AI Agent Systems
```

**Flywheel:** internal problem → Systems builds → used internally → measured →
standardized → client service → repeated → productized → platform capability.
Revenue Copilot is already on this path.

---

## 4. Map: Grok workspace → GitHub → infrastructure

### 4.1 Grok / operator workspace layout (Execution Platform v0 surface)

This is a **navigation + context layout for operators**, not a company
container. Each folder is a **domain scope** with least privilege.

| Path | Scope | Allowed context |
|---|---|---|
| `/00-command` | Holding / executive briefs | Strategy, capital, portfolio — read-mostly |
| `/01-systems` | AION Systems | Engineering + platform work |
| `/02-revenue` | Revenue workflows | CRM, outreach, proposals — tenant-scoped |
| `/03-media` | AION Media | Content / distribution |
| `/04-gstar` | G-Star | Label ops — **no** Systems secrets |
| `/05-assets` | Assets (stub) | Empty until Mission 001/002 pass |
| `/06-frontier` | Frontier (stub) | Experiments only; budget-capped |
| `/07-research` | Cross-cutting research | Read + recommend (L0–L1) |
| `/08-shared-services` | Capability invocations | Catalog stubs / runbooks |
| `/09-agent-registry` | Identity definitions | Agent cards only |
| `/10-service-catalog` | Service definitions | Versioned service specs |
| `/11-executions` | Execution Object views | IDs, status, cost — not chat dumps |
| `/12-evals` | Eval packs / scores | Domain-scoped |
| `/13-knowledge` | Scoped knowledge | GLOBAL / COMPANY / PROJECT / SESSION |
| `/14-policies` | Autonomy, approval, risk | Platform policy text |

**Hard rule:** Grok does not become a second OS. Grok **calls** AION Runtime.

```text
Wrong:  Grok → tools → systems
Right:  Grok → AION Execution API → policy → service/workflow → tools
```

### 4.2 GitHub repositories (systems of record)

| Repo | Plane role in v1 |
|---|---|
| `aion-docs` | Doctrine, ADRs, this spec, governance |
| `aion-core` | Contracts: Execution Object, Agent identity, policy, orchestration kernel |
| `aion-data` | Durable schema: `executions`, actors, runs, events, outcomes |
| `aion-runtime` | Composition root + **Execution Gateway HTTP** (ADR-003) |
| `aion-infra` | Deploy profiles, secrets boundary, observability plumbing |
| `aion-products` | Revenue Copilot / apps — **clients** of Runtime, not owners of control plane |
| `aion-desks` | Commercial packages — outside execution kernel |

### 4.3 Runtime providers (interchangeable)

```text
AION EXECUTION PLATFORM
Runtime Providers
├── Grok Bot          (elastic workers)
├── AION Runtime      (governed host — source of truth for runs)
├── OpenAI / Claude / Codex
├── Kimi / Atlas      (orchestration clients)
├── Local / specialized models
Tools (via catalog + MCP / APIs)
├── GitHub, Supabase, Vercel, VPS, Cloudflare, AWS
├── GoHighLevel, Stripe, Gmail
├── Notion, Airtable
└── MCP servers
```

AION owns the OS. Providers supply compute/intelligence.

### 4.4 Existing stack → future names

| Current | Future |
|---|---|
| AION Holding (Notion / doctrine) | Strategy Plane |
| AION Agent OS / Core contracts | Agent Control Plane |
| AION Runtime | Execution Plane host |
| AION Data / Supabase | Data Plane |
| AION Infra / VPS | Infrastructure Plane |
| Revenue Copilot | Revenue Application |
| Grok bots | Agent Runtime Provider |
| Kimi / Atlas | Orchestration Provider |
| Notion | Human knowledge / planning UI |
| GoHighLevel | Client / revenue UI |
| GitHub | Engineering system of record |

---

## 5. Agent hierarchy (roles, not bot sprawl)

```text
AION EXECUTION
Executive Operators (Holding-facing)
├── COO / CTO / CRO / CFO Analyst / Research Director
        ▼
Domain Orchestrators
├── Systems · Revenue · Media · G-Star · Assets · Frontier
        ▼
Specialist Agents
├── SWE · DevOps · Security · SDR · Copy · Analyst · …
        ▼
Skills / Tools  →  Deterministic Execution Gateway
```

**Beneath specialists:** deterministic infrastructure owns permissions, CI/CD,
policy, sandboxing, approvals, rollback.

```text
Agent decides: "We need to deploy."
        ↓
Execution Gateway checks: identity · company · project · env ·
  permission · cost · approval · rollback
        ↓
Tool executes.
```

That gateway **is** Runtime’s HTTP surface (ADR-003), not a new microservice.

### 5.1 Every agent card (required fields)

| Field | Example |
|---|---|
| ID / URI | `agent://aion/revenue/pipeline-ops/{id}` |
| Role | `pipeline-ops` |
| Owner | human/team accountable |
| Domain | `revenue` \| `systems` \| `media` \| `gstar` \| … |
| Mission / purpose | one job |
| Allowed tools | allow-list |
| Allowed data | least privilege scopes |
| Autonomy level | L0–L4 (see §8) |
| Budget | cost ceiling |
| Escalation policy | when to stop |
| Success metrics | eval ids / KPIs |

### 5.2 Every workflow (required fields)

Objective · Trigger · Inputs · Steps · Agents · Tools · Permissions ·
Approval gates · Output · Evaluation · Cost · Business KPI.

---

## 6. Canonical Execution Object

Atomic unit of machine labor. Already landing in Core/Data/Runtime Week 1 PRs.

Logical shape (see `@aion/core` `ExecutionObject` / `aion_execution`):

```text
Execution {
  execution_id, company_id / tenant_id, venture_id / domain,
  objective_id, workflow_id, agent_id / agent_uri,
  requested_by, input_context, tool_calls, permissions,
  budget, estimated_value, actual_cost, status, artifacts,
  outcome, revenue_impact, human_approval,
  started_at, completed_at, audit_trace
}
```

**North-star Holding question** (answered from execution records, not chat):

> What did the machine workforce accomplish this month, at what cost, what
> economic value, what failed, what improved, where to allocate?

---

## 7. Service Catalog (capability services, not departments)

Agents invoke **versioned capabilities**. They do not care which provider ran.

```text
AION SERVICE CATALOG (v0 examples — register only what Mission 001 needs first)

REVENUE
  revenue.lead.research@1
  revenue.lead.enrich@1
  revenue.lead.score@1
  revenue.outreach.generate@1
  revenue.followup.execute@1

ENGINEERING
  eng.repo.create@1
  eng.feature.build@1
  eng.test.run@1
  eng.preview.deploy@1

MEDIA (Mission 002)
  media.trend.research@1
  media.script.generate@1
  media.asset.produce@1
  media.post.schedule@1
  media.performance.analyze@1
```

Each service registration: Owner · Version · Inputs · Outputs · Permissions ·
Agent compatibility · Tools · Cost · SLA · Risk · Approval policy · Eval ·
Consumers.

**Catalog resolves:** service → workflow → permissions → agent/model → tools →
execution → eval.

---

## 8. Autonomy levels (implement immediately as policy)

| Level | Name | May do |
|---|---|---|
| **L0** | Observe | Read / search / analyze only |
| **L1** | Recommend | Plans / artifacts; **no** side effects |
| **L2** | Reversible execute | Branches, drafts, research, CRM enrich, reports, staging |
| **L3** | Execute with approval | Prod deploy, outbound comms, spend, customer-impacting |
| **L4** | Bounded autonomy | Earned only after evidence |

**L4 gate (illustrative):** success > 98%, policy violations < 0.1%, rollback
< 1%, known cost envelope, full observability, tested recovery.

Autonomy is declared on the agent and may be **tightened** per execution — never
raised by the worker.

---

## 9. Knowledge scopes (no giant shared context)

```text
AION KNOWLEDGE
GLOBAL     → doctrine, policies, companies, capabilities, architecture
COMPANY    → Systems | Media | G-Star | Assets | …
PROJECT    → Revenue Copilot | Client X | Album X | …
SESSION    → temporary execution context
```

Agents receive **minimum necessary context + minimum necessary permission**.
Grok workspace folders in §4.1 must mirror these scopes — not one mega-context.

---

## 10. Closed operating loop

```text
AION HOLDING → objectives
      → Company OS / missions
      → Execution Platform (decompose workflows)
      → Agent OS (assign agents + tools)
      → Runtime (execute)
      → Systems / tools (result)
      → Data OS (record everything)
      → Evals (measure)
      → Company OS (update decisions)
      → Holding (reallocate)
```

Flywheel: Goal → Plan → Execution → Tool calls → Artifact → Outcome → Cost →
Business result → Evaluation → Lesson → Updated workflow.

**Moat:** proprietary execution traces linking objectives → actions → costs →
outcomes → revenue — not “we use Grok.”

---

## 11. Non-goals (explicit)

- Do **not** make the second Grok workspace a holding company for all ventures.
- Do **not** create 12 parallel agent companies for marketing, crypto, robotics, etc. yet.
- Do **not** rebuild a second Execution Gateway outside Runtime (ADR-003).
- Do **not** product-local in-memory control planes as production path.
- Do **not** start Assets/Frontier infra before Mission 001 passes.
- Do **not** increase agent count before identity + permission registry exists.

---

## 12. Implementation backlog — 30 days

Aligned with Progress Assessment Weeks 1–4. Engineering targets remain:

> One real revenue workflow **survives restart, respects permissions, records
> cost, produces a measured outcome.**

Then:

> Multiple ventures invoke the same governed capability **without** sharing
> inappropriate context or permissions.

### Week 1 — Contracts + identity (in flight)

| ID | Work | Repo | Done when |
|---|---|---|---|
| W1.1 | Canonical Execution Object contract | `aion-core` | Schema + factory + tests |
| W1.2 | Agent identity URI + autonomy + allow-lists | `aion-core` | AgentActor fields + tests |
| W1.3 | Persist executions + actor identity columns | `aion-data` | Migration 0002 + repo |
| W1.4 | Gateway HTTP on Runtime (reconcile, don’t fork) | `aion-runtime` | `/v1/commands` etc. |
| W1.5 | ADR-003 + this spec | `aion-docs` | Merged / accepted |
| W1.6 | Grok workspace folders `/00`–`/14` as stubs | Operator surface | Layout exists; no mega-context |

### Week 2 — Catalog + Runtime contract + Grok adapter

| ID | Work | Repo | Done when |
|---|---|---|---|
| W2.1 | Service Catalog v0 (Mission 001 services only) | `aion-core` + `aion-data` | Versioned service records |
| W2.2 | Runtime enforces actor registry lookup on submit | `aion-runtime` | Unknown agent → reject |
| W2.3 | Grok adapter as Runtime **client** | `aion-runtime` / products | Submit via `/v1/commands` |
| W2.4 | Cost fields populated on every execution | core/data/runtime | Non-zero path tested |
| W2.5 | Autonomy L0–L3 policy hooks | `aion-core` | L3 requires approval gate |

### Week 3 — Mission 001 end-to-end (Revenue)

| ID | Work | Done when |
|---|---|---|
| W3.1 | Revenue Copilot submits to durable Runtime (not in-process Core) | Prod path uses Runtime |
| W3.2 | Permissions enforced on tools/data | Denied path tested |
| W3.3 | Approvals resume same run | Durable gate |
| W3.4 | Cost + telemetry + outcome attribution | Queryable from Data |
| W3.5 | Restart recovery still green | CI harness |

### Week 4 — Mission 002 (Media / G-Star)

| ID | Work | Done when |
|---|---|---|
| W4.1 | Register Media services/agents/workflows only | No Core redesign |
| W4.2 | Same Execution Object + catalog + Runtime | Cross-domain proof |
| W4.3 | Holding can ask workforce/cost/ROI from records | Dashboard or SQL view |

### Day-7 checklist (Phase I — this week)

- [ ] Second Grok environment renamed/treated as **Execution Platform v0**
- [ ] Folders `/00`–`/14` created; Assets/Frontier stubbed empty
- [ ] Every live agent has a filled identity card (§5.1)
- [ ] Every live workflow has fields in §5.2
- [ ] Merge order for Week 1 PRs: **core → data → runtime → docs**
- [ ] No new permanent agents without registry entry

---

## 13. Acceptance — Execution Platform v1

v1 is **proven**, not merely designed, when:

1. **Mission 001** passes (Revenue governed execution on Runtime).
2. **Mission 002** passes on the **same** primitives without core redesign.
3. Holding’s north-star question is answerable from `executions` + outcomes.
4. Grok (and at least one other provider) are Runtime clients.
5. Service Catalog is the invocation layer for those missions.
6. Knowledge/permissions remain domain-scoped under load.

---

## 14. References

- [ADR-001 Greenfield reset](../adr/ADR-001-greenfield-reset.md)
- [ADR-002 Runtime host ownership](../adr/ADR-002-runtime-host-ownership.md)
- [ADR-003 Execution Gateway into Runtime](../adr/ADR-003-execution-gateway-into-runtime.md)
- [execution-layer.md](execution-layer.md)
- [control-plane.md](control-plane.md)
- [../governance/agent-governance.md](../governance/agent-governance.md)
- [../roadmap/build-order.md](../roadmap/build-order.md)
- Notion: Execution Platform Progress Assessment (Sep 2026)
- Notion: Mission 001 / Mission 002
