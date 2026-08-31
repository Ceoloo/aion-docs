# Core Architectural Principles

These seven principles are **binding**. They are the compressed form of the
architecture; when a decision is unclear, decide in the direction these point.

---

## 1. Mission Before Infrastructure

**Do not create infrastructure because AION may eventually need it.**

Infrastructure must be justified by one of:

- an active mission,
- a current product requirement,
- a platform requirement,
- a risk-mitigation requirement, or
- a validated scaling constraint.

Avoid speculative distributed systems. A queue, a cache, a service mesh, a data
pipeline — none is built "to be ready." It is built when a real requirement
demands it, and the decision is recorded as an [ADR](../adr/README.md).

> Bad: *"AION must use Kafka."*
> Better: *"AION requires durable asynchronous event delivery once
> throughput/reliability requirements justify a broker. The implementation will
> be selected through an ADR."*

---

## 2. Orchestration Is Not Execution

**Deciding is not doing.** The orchestration layer decides what needs to happen,
which capability handles it, what context is required, what policies apply,
whether human approval is necessary, and how success is evaluated. Execution
workers actually perform the work.

Execution environments are interchangeable — native agents, Claude Code, Cursor,
Codex, Grok, Kimi/OpenClaw, API tools, deterministic services, human operators.
**AION must not become coupled to one model or agent runtime.** See
[../architecture/control-plane.md](../architecture/control-plane.md) and
[../architecture/execution-layer.md](../architecture/execution-layer.md).

---

## 3. Human Authority

**Humans retain ultimate authority.** Financial commitments, destructive
actions, gated production releases, customer-sensitive decisions, high-risk
external communications, security-sensitive configuration, and strategic
direction are decided or approved by humans, enforced through
[human gates](../governance/human-gates.md).

---

## 4. Explicit Ownership

**Every** business entity, schema, event, workflow, service, agent, product,
metric, permission, and API contract **has exactly one canonical owner.** Avoid
duplicate sources of truth. See
[../governance/authority-model.md](../governance/authority-model.md).

---

## 5. Events Represent What Happened

**Events describe completed facts.** Commands and intentions must not be
disguised as events.

| Good (facts) | Avoid (commands) |
|---|---|
| `lead.created`, `call.completed`, `proposal.sent`, `payment.received`, `deployment.failed` | `send.proposal`, `do.outreach` |

See [event-standards.md](event-standards.md).

---

## 6. Outcomes Power Learning

**Learn from real outcomes, not from model generations.** The learning system
connects the full chain:

> Context → Decision → Action → Outcome → Evaluation → Lesson → Recommendation →
> Updated Memory

Real-world outcome quality matters more than output volume. See
[../architecture/learning-loop.md](../architecture/learning-loop.md).

---

## 7. Products Are Built Through Missions

**No product without a mission.** A mission states the owner, problem, target
user, expected outcome, scope, explicit non-goals, success metrics, architecture
impact, data requirements, security considerations, release gate, validation
gate, and learning requirements. See
[../missions/lifecycle.md](../missions/lifecycle.md).

---

## The platformization corollary

Do not turn every successful product component into `aion-core`. A component
becomes a shared platform primitive **only when** (1) it is reused by multiple
products/missions, (2) its interface is understood, (3) its behavior is stable
enough to standardize, and (4) centralizing it reduces duplication more than it
creates coupling. See
[../roadmap/platform-maturity.md](../roadmap/platform-maturity.md#platformization-rule).
