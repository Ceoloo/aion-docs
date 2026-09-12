# Repository Map (Cursor orientation)

Canon details: [`../../repositories/`](../../repositories/).

## Workspace repos (inspected)

| Path | Purpose | Specialist owners |
|---|---|---|
| `aion-docs` | Architecture, governance, ADRs, `.aion` agent OS | Architect, Docs, Orchestrator |
| `aion-core` | Kernel: contracts, policy, approvals, orchestration ports | Runtime, AI, Security |
| `aion-data` | Postgres schema, migrations, repositories, outcomes | Data |
| `aion-runtime` | Host process, gateway HTTP, clients, image, proof matrices | Runtime, Infra |
| `aion-infra` | VPS/AWS/GCP profiles, deployment contract, policies | Infra, Security |
| `aion-products` | Revenue Copilot engines, CLI, web console, workforce-control | Product, Frontend, AI |
| `aion-desks` | Next.js Stripe landing for Operator/Everyday Desk | Product, Frontend, Integrations |

## Dependency direction (hard)

```
products → core → data
runtime  → core + data
infra deploys runtime image (no code cycle)
docs governs; not imported as code
```

Violations are **architecture defects**, not convenience exceptions.

## Where new work belongs (quick)

| If you are changing… | Put it in… |
|---|---|
| ADR / standard / agent OS / mission template | `aion-docs` |
| Permission, risk, approval, command/result contracts | `aion-core` |
| Tables, migrations, repositories, event persistence | `aion-data` |
| `/v1/commands`, health, container image, runtime clients | `aion-runtime` |
| Terraform/Compose, secrets injection, CI deploy | `aion-infra` |
| Copilot engines, product APIs, workforce UI logic | `aion-products` |
| Desk marketing/checkout pages | `aion-desks` |

## Known workspace note

`aion-desks` is present in the Cursor multi-root workspace but is **not listed**
in the six canonical repositories in `aion-docs`. Treat it as a product surface
until an ADR / repository ownership update formally includes it.
