# Testing

Testing establishes that **deterministic behavior is correct**. It is distinct
from [evals](evals.md), which measure the quality of **AI-driven,
non-deterministic** behavior. AION needs both.

## What must be tested

| Layer | Test focus |
|---|---|
| **Deterministic services** | Correctness of logic, edge cases, failure handling. |
| **Contracts** (API, event, data) | Conformance to the declared contract, including backward compatibility. |
| **Orchestration logic** | Routing, policy application, gate insertion — with execution mocked. |
| **Permissions & risk** | Denials, escalations, and gate triggering behave as specified. |
| **Data access paths** | Owned write paths enforce schema and ownership. |
| **Integrations** | Behavior against external systems, using fakes/contract tests. |

## Principles

- **Test behavior at the contract, not the implementation detail.** Contracts
  are the stable surface; internals may change.
- **Deterministic paths get deterministic tests.** Where a model is *not* in the
  loop, there is no excuse for untested behavior.
- **Failure paths are tested, not just happy paths.** A `deployment.failed` fact
  must be produced correctly when a deployment fails.
- **Gates and permissions are tested as security-relevant behavior.** A gate
  that can be bypassed is a defect, not a nuance.
- **Tests run in CI** and are part of the [Definition of Done](definition-of-done.md).

## The testing / eval boundary

- Use a **test** when the correct output is knowable and fixed (a function, a
  contract, a permission rule).
- Use an **eval** when quality is a judgment over model output (a summary's
  usefulness, a decision's soundness). See [evals.md](evals.md).

Do not paper over a non-deterministic quality question with a brittle exact-match
test, and do not use an eval where a deterministic assertion is possible.

## Deferred tooling

Specific test frameworks and CI runners are chosen per repository (`aion-infra`
owns CI/CD infrastructure). This standard governs *what* is tested and *to what
bar*, not *which tool*.

## Invariants

- **Deterministic behavior is tested, including failure paths.**
- **Contracts are conformance-tested for compatibility.**
- **Security-relevant behavior (gates, permissions) is tested.**
- **CI enforces it.**
