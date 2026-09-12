# AION Cursor Engineering Team

> Operating system for Cursor itself. Specialist agent chats behave like
> persistent engineering departments — not disposable chatbot sessions.

This directory lives in **aion-docs** (the architectural constitution) so agent
operating rules stay next to the contracts they enforce. It does **not** replace
[`architecture/`](../architecture/), [`engineering/`](../engineering/),
[`governance/`](../governance/), or [`missions/`](../missions/). Those remain
authoritative. `.aion/` is the **Cursor delegation layer** on top of them.

```
CEO / HUMAN
     ↓
AION ORCHESTRATOR          ← .aion/agents/00-orchestrator.md
     ↓
SPECIALIST AGENTS          ← .aion/agents/01–12
     ↓
CODE / TESTS / PRs         ← repos under ownership map
     ↓
REVIEW / APPROVAL          ← QA + Architect + Security + human gates
     ↓
DEPLOYMENT                 ← Infrastructure
     ↓
MEASURED OUTCOME           ← Data + Product + learning loop
```

---

## Workspace layout

```
.aion/
├── README.md                 ← you are here
├── TEAM.md                   ← responsibility matrix + chat conventions
├── agents/                   ← specialist role cards + starter prompts
├── standards/                ← Cursor-facing engineering rules (links to canon)
├── architecture/             ← system + repository maps for fast orientation
├── missions/                 ← agent-mission template (execution overlay)
├── handoffs/                 ← cross-agent dependency protocol
└── research/                 ← RESEARCH desk: briefs, backlog, PoC pointers
```

**Canonical repos in this Cursor workspace**

| Repo | Role |
|---|---|
| `aion-docs` | Constitution, ADRs, standards, this OS |
| `aion-core` | Control-plane kernel (policy, approvals, orchestration contracts) |
| `aion-data` | Canonical Postgres schemas, migrations, repositories |
| `aion-runtime` | Composition root, Execution Gateway, deployable image |
| `aion-infra` | VPS/AWS/GCP profiles, secrets, CI/CD, observability infra |
| `aion-products` | Revenue Copilot, Workforce Console, product logic + UIs |
| `aion-desks` | Desk landing/checkout (Stripe) — **not yet in the six-repo canon** |

---

## How to start tomorrow

1. Keep permanent chats named per [TEAM.md](TEAM.md#chat-naming-convention).
2. Paste the matching starter from [agents/STARTERS.md](agents/STARTERS.md).
3. Give large work to **AION — ORCHESTRATOR** first.
4. Orchestrator fills the [delegation protocol](#delegation-protocol), creates a
   [mission](missions/TEMPLATE.md) if needed, and routes to one primary owner.
5. Specialists finish with the [handoff standard](standards/agent-handoff.md).
6. Cross-agent needs use [handoffs/TEMPLATE.md](handoffs/TEMPLATE.md).

---

## Delegation protocol (Orchestrator)

When the human assigns a large task, the Orchestrator responds with:

```text
MISSION
OBJECTIVE
PRIMARY OWNER
SUPPORTING AGENTS
REVIEWERS
DEPENDENCIES
PARALLEL TASKS
ACCEPTANCE CRITERIA
RISKS
EXPECTED DELIVERABLE
```

Then routes work. The Orchestrator generally does **not** deep-implement when a
specialist owns the domain.

---

## Task ownership

Each mission has **one primary owner**. Others assist or review.

```text
MISSION:   Add durable execution recovery
OWNER:     Runtime Engineer
ASSIST:    Data Engineer
REVIEW:    QA Engineer, Security Engineer
ARCHITECTURE REVIEW: Architect
```

---

## Priority order

When choosing what to build, optimize for AION executing real business
workflows reliably:

1. revenue
2. execution
3. reliability
4. observability
5. reuse
6. client deployment speed

Cosmetic UI work loses to core execution capability until the execution path is
real, durable, and measurable.

---

## Reading order for any specialist

1. This README + [TEAM.md](TEAM.md)
2. Your agent card in [`agents/`](agents/)
3. [`../engineering/principles.md`](../engineering/principles.md)
4. [`../repositories/dependency-rules.md`](../repositories/dependency-rules.md)
5. [`architecture/repository-map.md`](architecture/repository-map.md)
6. Relevant repo README in the workspace
