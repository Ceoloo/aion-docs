# Product Module Contract (v0, Proposed)

How a product module plugs into the AION Workspace and Runtime. Decided by
[ADR-015](../adr/ADR-015-workspace-product-module-contract.md) (Proposed).
Creative OS is the first module; every later module (Wealth OS, Music Label
OS, and others) follows the same contract.

"MUST" items are required before a module may be marked `connected` in the
Workspace app registry.

## Parties

| Party | Owns | Never owns |
|---|---|---|
| **Workspace** (`aion-command-center`) | Shell, windows, launcher, Assistant, Missions/Approvals UI, SSO assertion issuing from Runtime-authenticated session | Execution, policy, product logic, product data |
| **Runtime** (`aion-runtime`) | Authentication, authorization, Service Catalog, executions, human gates, side-effect ledger, outcomes, module adapters | Product logic, product schemas, provider SDKs |
| **AION Data** (`aion-data`) | Governance records behind Runtime | Product/content entities |
| **Module** (e.g. `aion-creative-os-CVD`) | Product UI, product logic, product data store, its own workers | Approval authority, identity, cross-tenant decisions |

```
Browser ─▶ Workspace (BFF) ─▶ Runtime ─(module adapter)─▶ Module server API ─▶ Module workers
   │                                    │
   └─ module window (same parent domain, SSO handoff) ─▶ Module web app
                                        └─▶ AION Data (governance truth)
```

## 1. Manifest

A module MUST publish a manifest the Workspace registers in its app registry:

| Field | Rule |
|---|---|
| `appId` | Stable, lowercase, e.g. `creative-os` |
| `kind` | `remote_module` for web modules; `local_native` only via the Native Companion |
| `availability` | Starts `planned`. Becomes `connected` only after section 10 evidence passes **for every advertised gated `serviceKey`** |
| `href` | Module origin on a subdomain of the Workspace's parent domain |
| `subApps` | Optional list of the module's apps (label, path, description) |
| `health` | Liveness and readiness URLs |
| `services` | `serviceKey`s the module implements |

## 2. Identity and tenancy

- The module MUST NOT accept tenant, actor or role from the browser.
- **SSO handoff:** the Workspace server issues a short-lived signed assertion
  (minutes, single audience = the module) only for a principal that Runtime has
  already authenticated in that request path. Claims (`tenantId`, `actorId`,
  `principalId`, roles) MUST be copied from the Runtime-authenticated session /
  durable actor grants — not from client-supplied fields or a stale local map.
  The module verifies the assertion **server-side** (signature, audience,
  expiry) and creates its own session. Optionally, the module MAY exchange the
  assertion with Runtime for a fresh validation before creating the session.
  The signing technology is not fixed by this contract.
- Module product rows MUST carry the Runtime `tenantId` and be isolated by it
  (row-level policies or equivalent), so one tenant cannot read another's data.
- Operators act with `invoke` for a tenant. Approvers are **registered human
  actors** with `approve` in that tenant ([ADR-005](../adr/ADR-005-runtime-identity-plane.md)).
  For client-gated actions the approver is the client's own principal.

## 3. Services

- Each governed capability MUST be a Service Catalog entry with a versioned
  `serviceKey` (`name@version`, e.g. `creative.short.render@1`).
- Each entry declares an input schema, a [risk level](../governance/risk-levels.md)
  and an [action tier](../governance/action-tiers.md).
- Schemas are generated from the module's own validators so the module and the
  catalog cannot drift.
- Changing a service's input or behaviour incompatibly means a new version, not
  an in-place edit.

## 4. Module adapter (Runtime side)

- Runtime reaches the module only through a module adapter that follows the
  GHL adapter pattern: backend policy, a fake backend for tests, production
  write eligibility checks, normalized provider errors.
- Every module write is recorded in the external side-effect ledger with an
  idempotency key. The **same stable idempotency key MUST cross the adapter
  boundary** into the module API (same idea as the harness seam in
  [ADR-011](../adr/ADR-011-harness-execution-seam.md)). The module MUST treat
  that key as the de-duplication key and, for repeated or concurrent requests
  with the same key, atomically return the original result. Retrying the same
  command MUST NOT create a second artifact even if the Runtime ledger write
  was lost after the module succeeded.
- The adapter authenticates to the module with a server-only, tenant-scoped
  credential. No module credential reaches a browser.
- **Status reporting:** Runtime MUST receive job status through **either**
  polling **or** callbacks (or both). The module MUST implement at least one of:
  a pollable status endpoint, or a callback Runtime can verify. The mechanism
  is chosen during the first adapter build; this contract does not require both.

## 5. Approvals

- Runtime owns the gate, approver identity, decision and audit.
- The gate's proposed action MUST include the module's **revision id** and
  **content hash** for the thing being approved (for example an edit plan),
  plus a preview reference.
- The module MUST execute a gated action only when its persisted revision/hash
  **and** the Runtime approval id both match. It stores the approval id against
  the revision.
- Any edit after approval creates a new revision. The old approval no longer
  matches and the action is refused.
- Approval decisions happen in the Workspace Approvals app. The module UI may
  link to the approval but MUST NOT offer its own approve button for gated
  actions.

## 6. Progress, failures and outcomes

- Module jobs surface in Workspace Missions through Runtime executions:
  queued, running, succeeded or failed, with constant error codes.
- A failed or partial job MUST NOT be reported as complete.
- On completion Runtime records the outcome. Value claims follow
  [ADR-014](../adr/ADR-014-outcome-based-systems-engineering.md): no positive
  value without an evidence reference.

## 7. Artifacts

- Outputs (renders, files, reports) stay in the module's store under immutable,
  tenant-scoped paths.
- Runtime records an **artifact reference** (module, id, content hash), not a
  URL. Viewing goes through a short-lived signed URL issued on request. Signed
  URLs are never persisted in Runtime or AION Data.

## 8. Data ownership

- Product data lives in the module's store; governance data lives in AION Data.
  Each side stores the other's ids as references and never copies canonical
  records. This amends the earlier blanket rule that every canonical entity
  lives in `aion-data` for **product-module satellites** under this contract
  (see [ADR-015](../adr/ADR-015-workspace-product-module-contract.md)). Platform
  business entities (leads, outcomes, missions, approvals, side effects) remain
  in AION Data.
- The module MUST NOT import `@aion/core` or `@aion/data`, and MUST NOT write to
  AION Data directly.
- Untrusted content (uploads, transcripts, scraped posts) is data, never
  instructions, and cannot change permissions or execution policy.

## 9. Embedding in the Workspace

- The module opens in a Workspace window (`remote_module`). It is served from a
  subdomain of the Workspace's parent domain so cookies are same-site.
- The module MUST restrict framing to the Workspace origin (CSP
  `frame-ancestors`).
- Window messages use `postMessage` with origin checks, limited to:
  `module.ready`, `module.title`, `module.attention` (badge count),
  `workspace.openApproval` (approval id), `workspace.openMission` (mission id).
  Messages never carry credentials or tenant authority.
- The module also works in its own browser tab for heavy work, using the same
  SSO handoff.

## 10. Acceptance evidence (before `connected`)

One real, tenant-bound run **per advertised gated service**, recorded in the
[operator-loop evidence format](https://github.com/Ceoloo/aion-runtime/blob/main/docs/operator-loop-v1.md).
The module stays `planned` until **every** advertised gated `serviceKey` has a
passing pack. Ungated catalog entries used in production flows SHOULD also have
an evidence row, but do not alone unlock `connected`.

| Claim | Evidence |
|---|---|
| Governed submit | Mission id, execution id, `serviceKey`, tenant |
| Human gate | Approval id, proposed action with revision id + hash, approver actor |
| Dual check | Module log showing revision/hash and approval id matched before execution |
| Rejection path | A denied gate produces no side effect and no artifact |
| Edit invalidation | An edit after approval is refused until re-approved |
| Side effect | Ledger entry with idempotency key; module returns original result on replay |
| Outcome | Outcome id with truthful status and evidence reference |
| Isolation | A principal of tenant B cannot read, run or approve tenant A's work |

## Invariants

- One authority: Runtime decides; modules execute.
- One inbox: approvals are decided in the Workspace.
- No product tables in AION Data; no governance truth in module stores.
- No identity or tenant from the browser; SSO claims come from Runtime-authenticated authority.
- Idempotency keys cross the adapter; modules de-duplicate.
- `planned` until every advertised gated service is proven.
