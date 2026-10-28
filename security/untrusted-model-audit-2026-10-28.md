# AION untrusted-model boundary audit — 2026-10-28

## Scope and assurance limits

Targeted source review prompted by https://youtu.be/3mx84MRj8lY. This is not a
complete penetration test, production attestation, model scan or dependency audit.
No live host, model artifact, secret store, firewall or deployed image was inspected.
No production setting changed. Changes described below are proposed until merged
and deployed. Readiness requires deployment evidence, not this document alone.

Reviewed snapshots:

| Repository | Commit | Reviewed surfaces |
|---|---|---|
| aion-runtime | `084dea52e1436daa9d0e9ce1fcf0c4372cc6f905` | HTTP server, auth/config, identity/approval tests, dependency setup, CI, CRM HTTP adapters |
| aion-core | `e078fe0917fb5eedf0671c9d642a0220901ef5c7` | Tool permission and risk/policy entry points |
| aion-infra | `fc9e3e106b33c236d98f24cffa60d170f5118e79` | VPS Compose, security/policy documentation, model-runner references |
| aion-action-engine | `101f9c5481f062fdb314207057d89395c8df66e0` | Principal binding, role permissions and action-engine methods |
| aion-docs | `0de8970b3cebe129a7afcbb2cc74c646ee67bf42` | Security model/baseline, model router, agent governance and Definition of Done |

Runtime validation builds its own pinned Core `7eca6a208224fda0de00e8549ba73d3542b2f2bb`
and Data `946ca4bfdeba41e025f2e4f57b97a4a8cfb5e5db`; standalone Core's current main
is not automatically what Runtime executes.

## Findings

| ID / priority | Evidence and consequence | Disposition |
|---|---|---|
| SEC-01 / high when local open mode is reachable | `src/auth/config.ts` defaults local to open; `src/server.ts` called `listen(port)` without a host. Open proof-mode gateway could listen beyond loopback. | Patch open mode to bind `127.0.0.1`; authenticated mode remains container/proxy accessible. CI regression added. |
| SEC-02 / high in open mode | `src/server.ts` checked CORS only for OPTIONS, and had no Host validation. Unapproved simple browser requests could reach handlers; binding loopback alone would not address DNS rebinding. | Exact local Host/port validation and origin rejection before health, preflight or gateway dispatch. Opaque origins and cross-site Fetch Metadata without Origin are denied. CI exercises real HTTP. |
| SEC-03 / release gate for new tool-enabled models | Model-router documentation defers runner choice; no model artifact/template admission mechanism was identified in the reviewed paths. Popularity and hashes alone cannot establish benign behavior. | UMS-01–03 define admission evidence. Intelligence/TechOps must implement/inventory before tool-enabled local-model promotion. This audit does not assert there is no implementation elsewhere. |
| SEC-04 / high before untrusted code execution | VPS service network named `internal` is a normal bridge, not `internal: true`; runtime and copilot also join the edge network. Reviewed Compose does not demonstrate restricted egress, read-only root filesystem or dropped capabilities. | Infra must verify effective network/mount/identity policy and broker separation. Do not blindly enable `internal: true`: business adapters need deliberate connectivity. Proposed milestone: before any arbitrary-code tool is enabled. |
| SEC-05 / supply-chain follow-up | `scripts/setup-deps.mjs` pins refs by default, but accepts repository/ref environment overrides, interpolates them into shell commands and skips setup when `dist/index.js` exists. An existing build is not revalidated against the pin. | Runtime owner: replace shell interpolation with argument vectors, validate trusted source/ref overrides and verify cached source/build provenance. Build inputs must remain trusted meanwhile. No demonstrated remote exploit is claimed. |
| SEC-06 / outbound follow-up | Reviewed GHL/Twenty adapters call fetch without an explicit redirect policy; Twenty base URL is operator-configured. Redirects are not currently proven to stay within an approved destination/resource policy. | Runtime/Infra: add redirect-denial tests and explicit transport policy before extending connector exposure. This is not proof of token leakage: fetch implementations can strip cross-origin authorization. |

## Existing strengths verified in source/tests

- Staging/production auth defaults to required and explicitly rejects open mode.
- Runtime binds authenticated principals and tenant subsets; durable actor grants
  defeat body self-assertion. Human approvals reject impersonation/non-human actors.
- Core checks explicit tool permissions and combines risk signals.
- Action Engine derives permissions from server-side bearer bindings and omits
  approval permission from its agent role. This is not a full tenant-isolation review.
- VPS runtime has no published host port in reviewed Compose; it uses an edge
  network. Migration credentials are excluded from its explicit environment list.
- Canonical architecture already requires gateway-owned execution, least privilege,
  sovereignty and revocation. UMS makes model compromise an explicit design input.

## Validation performed

- Runtime `npm run typecheck`: passed.
- Runtime `npm run test:network-boundary`: 4 tests passed, including real HTTP
  Host/origin/preflight/simple-POST rejection before database work and allowed
  local/development-origin requests.
- Runtime `npm run test:gateway`: 49 existing tests passed, including identity,
  cross-tenant denial, approval binding, registry/revocation and concurrency.
- Production readiness, Docker boot certification, database acceptance, actual
  off-host network reachability and model-artifact inspection: **not run here**.

## Deployment and closure checklist

1. Merge normal reviewed changes; run the full Runtime CI/boot certification.
2. Deploy to staging and confirm the image/commit identity. Required-auth mode
   must remain reachable through the authenticated edge; unauthenticated gateway
   requests must fail. Local open mode must not accept LAN/container-peer traffic.
3. Verify browser Console origin configuration and run existing mission proofs.
4. Record effective inference/worker credentials, mounts, outbound routes and
   runner/model/template inventory. Use synthetic secret canaries, not real secrets.
5. Assign SEC-03–06 to the named owning teams with dates in the normal backlog.
   These are open actions, not completed controls or already-created tickets.
6. Promote production through existing release governance. Roll back to the prior
   immutable image if needed while keeping open-mode endpoints isolated.

See [Untrusted Model Security](../architecture/untrusted-model-security.md) for
UMS-01–08, the permanent builder gate and containment requirements. A successful
model eval or this patch alone must never be presented as “AION is fully secure.”
