# Evals

Evals measure the quality of **AI-driven, non-deterministic** behavior — the
soundness of a decision, the usefulness of a generation, the reliability of an
agent — where a fixed assertion cannot. They are the quality backbone of the
[intelligence layer](../architecture/intelligence-layer.md) and a precondition
for the [learning loop](../architecture/learning-loop.md).

## Why evals, not just tests

A model producing more output is **not** evidence of quality
([Principle 6](principles.md)). Evals judge whether output is actually good:
did the decision hold up, was the summary faithful, did the agent stay in scope.
Without evals, "it ran" masquerades as "it worked."

## What gets evaluated

| Target | Example eval question |
|---|---|
| **Agent output** | Did the agent produce a correct, in-scope, policy-respecting result? |
| **Decisions** | Was the routing/decision the right one given the context? |
| **Generations** | Is the generated content faithful, useful, and safe? |
| **Workflows** | Does the end-to-end workflow produce good outcomes over a set? |

Every agent carries **evaluation criteria** in its spec. See
[../governance/agent-governance.md](../governance/agent-governance.md).

## Requirements

- **Eval criteria are explicit and owned.** They live with the agent/capability
  they judge.
- **Evals run against representative sets**, not a single cherry-picked example.
- **Eval results are durable records** in `aion-data` and feed the
  [learning loop](../architecture/learning-loop.md).
- **Outcomes outrank generations.** Where a real-world outcome is available
  (`payment.received`, `deal.lost`), it is the strongest eval signal —
  stronger than any judgment of the generation itself.
- **Regression is caught.** A capability's eval performance is tracked over time;
  a drop blocks promotion.

## Evals in the readiness bar

AI-driven behavior is not [production-ready](production-readiness.md) without
eval criteria and results. This is not optional for agents.

## The test / eval boundary

- **Test** deterministic, knowable-correct behavior. See [testing.md](testing.md).
- **Eval** non-deterministic, judgment-based quality.

## Deferred tooling

The eval harness/framework is implemented in `aion-core`; specific tooling is
chosen there. This standard governs *that* AI behavior is evaluated and *how the
results are used*, not the tool.

## Invariants

- **AI-driven behavior has explicit, owned eval criteria.**
- **Real outcomes outrank generation judgments.**
- **Eval results are durable and feed learning.**
- **Eval regressions block promotion.**
