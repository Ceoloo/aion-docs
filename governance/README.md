# Governance

Governance defines **who is allowed to do what, under which controls, and when a
human must decide.** AION is a governed system, not an autonomous one: humans
retain ultimate authority, agents operate under permissions, and important
actions can require human gates.

## Documents

| Document | Purpose |
|---|---|
| [authority-model.md](authority-model.md) | Where authority and ownership sit. |
| [human-gates.md](human-gates.md) | When a human must approve. |
| [agent-governance.md](agent-governance.md) | Agents as governed workers. |
| [permissions.md](permissions.md) | The permission model. |
| [risk-levels.md](risk-levels.md) | How actions are classified by risk. |
| [autonomy-tiers.md](autonomy-tiers.md) | Graduated autonomy: auto / monitor / approve / deny. |
| [change-management.md](change-management.md) | How changes reach production. |

## The governance stance

- **Humans retain ultimate authority.** Financial, destructive, strategic, and
  high-risk actions are theirs to approve.
- **Least privilege by default.** Every identity gets only what its work needs.
- **Explicit ownership.** One canonical owner per entity, workflow, agent,
  metric, and permission.
- **Observability underwrites governance.** You cannot govern what you cannot
  see. See [../architecture/observability.md](../architecture/observability.md).
- **Autonomy is earned per workflow, within policy** — never assumed. See
  [../roadmap/platform-maturity.md](../roadmap/platform-maturity.md).
