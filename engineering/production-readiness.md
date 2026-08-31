# Definition of Production Readiness

"Production-ready" in AION is a **defined bar**, not a feeling. A capability,
service, agent, or product is production-ready only when it satisfies every
requirement below. If it does not, it is a prototype — which is fine, but it does
not run in production.

## The production-readiness checklist

A thing is production-ready when:

- [ ] **It serves a mission.** Its existence traces to a mission or stated
  requirement. ([Mission Before Infrastructure](principles.md))
- [ ] **It has a single canonical owner.** Human/team accountability is
  explicit. ([ownership](../governance/authority-model.md))
- [ ] **Its boundaries are correct.** It lives in the right repository and
  respects the [dependency rules](../repositories/dependency-rules.md).
- [ ] **Its contracts are explicit.** Input/output, events, and any API/data
  contract are defined to standard. ([api](api-standards.md),
  [event](event-standards.md), [data](data-contracts.md))
- [ ] **It is permissioned least-privilege.** Only the tools and data it needs.
  ([permissions](../governance/permissions.md))
- [ ] **Its risk is classified** and high-risk actions are
  [gated](../governance/human-gates.md). ([risk](../governance/risk-levels.md))
- [ ] **It is observable.** It emits the traceability spine.
  ([observability](observability-standards.md))
- [ ] **It is tested** to the [testing standard](testing.md).
- [ ] **For AI-driven behavior, it is evaluated.** It has eval criteria and
  results. ([evals](evals.md))
- [ ] **It has cost controls** (for agents/model use): budget, limits,
  escalation. ([agent governance](../governance/agent-governance.md))
- [ ] **It fails safe.** Failures are handled, observable, and — for destructive
  actions — reversible or gated.
- [ ] **It reached production through change management.**
  ([change management](../governance/change-management.md))
- [ ] **It meets the [Definition of Done](definition-of-done.md).**

## Agents specifically

An agent is production-ready only with a complete
[agent specification](../governance/agent-governance.md): ID, purpose, owner,
capabilities, allowed tools, allowed data, forbidden actions, risk level,
escalation conditions, input/output contracts, evaluation criteria, cost
controls, and observability requirements.

## What production-readiness is NOT

- It is **not** "the code runs." Running is necessary, not sufficient.
- It is **not** "the model produced output." Output volume is not quality.
- It is **not** maximum autonomy. A production-ready capability may be
  deliberately gated. See [../roadmap/platform-maturity.md](../roadmap/platform-maturity.md).

## Invariant

**Production readiness is the whole checklist, or it is not production
readiness.** Partial compliance means prototype status — labelled as such.
