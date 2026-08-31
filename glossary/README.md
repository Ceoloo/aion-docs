# Glossary

Canonical terminology for AION. When a term is used across the documentation, it
means what it means here. Terminology is owned by `aion-docs` — one definition
per term, consistent with [explicit ownership](../governance/authority-model.md).

## System & layers

| Term | Definition |
|---|---|
| **AION** | An AI-native company and product operating architecture: a governed system for building, operating, and learning from products, with humans holding ultimate authority. |
| **Company OS** | The operating substrate of AION — identities, policies, mission registry, org context. Home: `aion-core`. |
| **Control plane / Orchestration** | The decision layer: decides what happens, who does it, what context/policy apply, whether a gate is needed, how success is judged. Deciding, not doing. |
| **Execution layer** | The doing layer: agents, external runtimes, services, and humans that perform work. |
| **Intelligence layer** | Context, memory, reasoning, learning, and evals. |
| **Data layer** | Canonical data, events, and derived stores. Home: `aion-data`. |
| **Product layer** | Products built on the platform. Home: `aion-products`. |
| **Learning loop** | Context → Decision → Action → Outcome → Evaluation → Lesson → Recommendation → Updated Memory. |

## Governance

| Term | Definition |
|---|---|
| **Human authority** | The principle that humans decide/approve high-risk and strategic actions. |
| **Human gate** | A runtime pause where a human must approve before a sensitive action proceeds. Fails safe. |
| **Risk level** | Classification (R0–R3) that drives autonomy, gating, and observation. |
| **Permission** | An explicit, owned grant of allowed tools/data/actions to an identity. Least privilege. |
| **Agent** | A governed worker with an explicit specification (ID, owner, scope, forbidden actions, cost controls, evals, observability). Not a free-roaming personality. |
| **Canonical owner** | The single repository/team accountable for defining and changing a given entity, contract, or artifact. |

## Data

| Term | Definition |
|---|---|
| **Operational state** | Current business/system state — the "now". |
| **Event** | An immutable, past-tense fact of what happened. Not a command. |
| **Memory** | Useful retained context for future work. |
| **Lesson** | A validated conclusion derived from real outcomes. |
| **Recommendation** | Forward-looking guidance derived from lessons. |
| **Analytics** | Aggregations used for decision-making, recomputable from events. |
| **Outcome** | The real-world result of an action, recorded distinctly. |
| **Data contract** | The explicit, owned, classified definition of a piece of data and its evolution rules. |
| **Lineage** | The traceable path from a derived record back to the events it came from. |

## Missions & delivery

| Term | Definition |
|---|---|
| **Mission** | The unit through which products/capabilities are built: owner, problem, user, outcome, scope, non-goals, gates, learning. |
| **Release gate** | The gate proving a mission is production-ready and governed before shipping. |
| **Validation gate** | The gate proving a shipped mission delivered its expected real-world outcome. |
| **Platformize** | Promoting a proven, reused component to a platform primitive — only when all four conditions of the platformization rule hold. |
| **Production readiness** | The full checklist a capability must satisfy to run in production. |
| **Definition of Done** | The full checklist a change must satisfy to be considered finished. |

## Architecture governance

| Term | Definition |
|---|---|
| **ADR** | Architecture Decision Record — a durable record of a significant decision and its consequences. |
| **Drift** | Accumulated architectural ambiguity: overlapping responsibilities, duplicated truth, blurred boundaries. The thing AION is designed to prevent. |
| **Carry-forward decision** | An explicit, ADR-backed decision to bring a specific concept forward from legacy — deliberately redesigned, not copied. |
| **Maturity (L0–L5)** | The stages from Documented to Adaptive; measures reliable-outcomes-with-governance, not raw autonomy. |

## Legacy

| Term | Definition |
|---|---|
| **Aion-Sys** | The legacy GitHub organization holding prior AION R&D. Historical reference only — never a dependency. See [../legacy/README.md](../legacy/README.md). |
| **Legacy / reference material** | Prior work that may inform design but is not imported, copied, or depended upon. |
