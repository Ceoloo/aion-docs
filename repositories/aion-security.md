# aion-security

**Role:** TechOps security satellite — blue Continuous Assurance operations and
red adversarial harnesses. Client of the Runtime Execution Gateway.

> **Canon note:** like [`aion-desks`](aion-desks.md), this is a **workspace
> satellite**, not one of the six canonical platform repositories.
> **[ADR-020 (Accepted)](../adr/ADR-020-aion-security-repo-ownership.md)** makes
> it a permanent security-ops satellite. Do not put control-plane, canonical
> schema, or Execution Gateway logic here.

## Owns

- Continuous Assurance **connectors** and local runners (SIS-CA check subset)
- Red **Observe-tier** adversarial playbooks (default-deny + allow-list)
- Security agent manifests (registration intent; Runtime registry is authority)
- Evidence packaging helpers for TechOps audits / Continuous Assurance reports

## Does NOT own

- Company OS / orchestration / PolicyEngine (`aion-core`)
- Canonical data / events (`aion-data`)
- Runtime host / Execution Gateway / identity plane (`aion-runtime`)
- Cloud provisioning / secrets architecture (`aion-infra`)
- Normative SIS / SEL / Continuous Assurance contracts (`aion-docs`)
- Product operator UIs (`aion-products`)

## Integration posture

| Seam | Rule |
|---|---|
| Runtime | HTTPS + Bearer + tenant; staging/prod fail closed |
| Assurance authority | Prefer Runtime `/v1/assurance/*` as platform surface; this repo adds delivery connectors |
| Remediations | Human-gated; future Execute paths must use `POST /v1/commands` |
| Red team | `AION_RED_ENABLED` + allow-list; no foundation Execute manifests |
| Infra | Host hardening stays in `aion-infra`; this repo does not provision |

## Specialist owners

Primary: TechOps / Security Infrastructure. Assist: Runtime (gateway contracts),
Documentation (SIS/CA). See [`.aion/TEAM.md`](.aion/TEAM.md) when updated.

## Related

- [dependency-rules.md](dependency-rules.md)
- [../architecture/continuous-assurance-v1.md](../architecture/continuous-assurance-v1.md)
- [../architecture/secure-execution-layer-v1.md](../architecture/secure-execution-layer-v1.md)
- [../offers/techops-security-operations-lite.md](../offers/techops-security-operations-lite.md)
