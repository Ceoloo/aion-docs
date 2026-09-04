# Engineering Standards

The rules code must satisfy to belong in AION. These standards are binding
across all repositories; they turn the architecture into buildable, verifiable
contracts.

## Documents

| Document | Purpose |
|---|---|
| [principles.md](principles.md) | The core architectural principles. |
| [production-readiness.md](production-readiness.md) | What "production-ready" means. |
| [testing.md](testing.md) | Testing expectations. |
| [evals.md](evals.md) | Evaluating AI-driven quality. |
| [observability-standards.md](observability-standards.md) | Emitting the traceability spine. |
| [agent-trace-schema.md](agent-trace-schema.md) | OpenTelemetry-compatible mapping of the trace spine. |
| [api-standards.md](api-standards.md) | How APIs are designed. |
| [event-standards.md](event-standards.md) | Events as past-tense facts. |
| [data-contracts.md](data-contracts.md) | Defining and evolving data contracts. |
| [definition-of-done.md](definition-of-done.md) | When work is actually finished. |

## Posture

- **Standards define contracts, not vendors.** When a technology is unchosen,
  the standard names the required capability and defers the choice to an ADR.
- **Standards are enforceable.** A standard you cannot check in review or CI is
  a wish, not a standard.
- **Standards serve the architecture.** They exist to keep the
  [boundaries](../repositories/dependency-rules.md) and
  [principles](principles.md) intact.
