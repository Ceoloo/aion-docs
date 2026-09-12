# Security Standards (Cursor agents)

Canon: [`../../architecture/security-model.md`](../../architecture/security-model.md),
[`../../governance/permissions.md`](../../governance/permissions.md),
[`../../governance/human-gates.md`](../../governance/human-gates.md),
[`../../governance/agent-governance.md`](../../governance/agent-governance.md).

## Principle

Agents receive the **minimum authority** required to complete a mission.
Sensitive execution supports **human approval**.

## Hard rules

- Least privilege for tools, data, and service keys.
- Never commit credentials, tokens, private keys, or `.env` files.
- Never put secrets in browser/`NEXT_PUBLIC_*` unless they are publishable by design.
- Runtime app role (`aion_app`) must never receive migrator credentials.
- Tenant isolation is non-negotiable for multi-tenant data.
- High-risk actions use Core risk levels + approval gates (R0–R3).
- Do not bypass the control plane for governed actions.
- Auditability: sensitive actions leave events / execution records.

## When to involve Security Engineer

- New permissions, roles, or capability grants
- Approval-gate changes
- Secrets handling / CI credential wiring
- External OAuth or webhook verification
- Tenant isolation or cross-tenant query risk
- Any production “break glass” / privileged path
