# Definition of Done

"Done" is a shared, checkable bar — not "it works on my machine." A change is
**done** when it satisfies every item below. This is narrower than
[production readiness](production-readiness.md) (which qualifies a *capability*);
Definition of Done qualifies a *change*.

## A change is done when:

- [ ] **It serves a stated need.** It traces to a mission, requirement, review
  comment, or defect — not scope invented mid-flight.
- [ ] **It is in the right place.** It respects
  [repository boundaries and dependency rules](../repositories/dependency-rules.md).
- [ ] **Contracts are honored.** Any API/event/data contract it touches is
  updated to standard, additive-safe or properly versioned.
  ([api](api-standards.md), [event](event-standards.md), [data](data-contracts.md))
- [ ] **It is tested.** Deterministic behavior — including failure paths — is
  covered and passes in CI. ([testing](testing.md))
- [ ] **AI behavior is evaluated.** If it changes model-driven behavior, evals
  exist and do not regress. ([evals](evals.md))
- [ ] **It is observable.** New significant actions emit the traceability spine.
  ([observability](observability-standards.md))
- [ ] **It is governed.** Permissions, risk classification, and any required
  gates are in place. ([permissions](../governance/permissions.md),
  [risk](../governance/risk-levels.md), [gates](../governance/human-gates.md))
- [ ] **No secrets committed.** No credentials in code or docs — ever.
  ([security](../architecture/security-model.md))
- [ ] **It fails safe.** Errors are handled and observable; destructive actions
  are reversible or gated.
- [ ] **Significant decisions are recorded.** Architecturally significant choices
  have an [ADR](../adr/README.md).
- [ ] **Docs reflect reality.** If it changes a contract or boundary, the
  relevant doc in `aion-docs` is updated in the same change.

## Not done if…

- tests are skipped or failing;
- an eval regressed and was ignored;
- a contract changed silently;
- a secret was committed;
- a boundary was crossed "just this once";
- the change works but is untraceable.

## Invariant

**Done means the whole checklist.** "It runs" is one line of it, not the whole.
