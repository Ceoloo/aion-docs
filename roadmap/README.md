# Roadmap

How AION is built over time — the **order** in which the platform is assembled
and the **maturity model** by which its autonomy is measured. Both are designed
to resist the speculative accretion that caused the
[reset](../adr/ADR-001-greenfield-reset.md).

## Documents

| Document | Purpose |
|---|---|
| [build-order.md](build-order.md) | The phased order in which repositories/capabilities are built. |
| [platform-maturity.md](platform-maturity.md) | The L0–L5 maturity model and the platformization rule. |
| [operating-leverage.md](operating-leverage.md) | **Post-v0.2.0 chapter** — OL-001–005 + UX-001 Operator Console. Business ops drive engineering. |
| [ops-001-live-runtime.md](ops-001-live-runtime.md) | **OPS-001** — first live Runtime on VPS behind Traefik before Vercel Console. |
| [ie-001-implementation-engine.md](ie-001-implementation-engine.md) | **IE-001** — one client, one repeatable implementation, one live workflow. |
| [ie-002-provisioning-activation.md](ie-002-provisioning-activation.md) | **IE-002** — provisioning readiness + human activation gate (OL-001 feeder). |
| [secure-automation-deployment-standard.md](secure-automation-deployment-standard.md) | **SA-STD-001** — Secure Automation deployment standard + Lead-to-Appointment v1 (OL verification map). |

## The three ideas

- **Build order** answers *"what do we build next, and in what sequence?"* — and
  insists that each phase implements only what current missions require.
- **Platform maturity** answers *"how autonomous is AION, and how does a
  capability become a platform primitive?"* — and insists that maximum autonomy
  is **not** the goal.
- **Operating leverage** answers *"how much useful business work can AION
  reliably execute per dollar, per human approval, and per hour?"* — and is the
  north star after `execution-platform-v0.2.0`.
