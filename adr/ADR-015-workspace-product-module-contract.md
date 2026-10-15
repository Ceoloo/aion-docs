# ADR-015: Workspace Product Module Contract; Creative OS as the First Module

- **Status:** Proposed
- **Date:** 2026-10-15
- **Decision Owners:** CEO LOO (accountable); Architect (review)
- **Related:** [ADR-002](ADR-002-runtime-host-ownership.md),
  [ADR-004](ADR-004-aion-desks-repo-ownership.md),
  [ADR-005](ADR-005-runtime-identity-plane.md),
  [ADR-007](ADR-007-observe-assist-execute-tiers.md),
  [ADR-013](ADR-013-command-center-configuration-authority.md),
  [ADR-014](ADR-014-outcome-based-systems-engineering.md),
  [`../architecture/product-module-contract.md`](../architecture/product-module-contract.md),
  [`../repositories/aion-creative-os.md`](../repositories/aion-creative-os.md)

## Context

The AION Workspace (`aion-command-center`, the Web Desktop shell) is becoming
the surface where AION operators and clients run governed business work. Its
app registry already distinguishes `native_workspace`, `remote_module`,
`external_shortcut` and `local_native` apps, and the dashboard blueprint lists
"product satellites" (Wealth OS, Music Label OS, Creative OS, Desks, Growth
Funnel) to be integrated **"by module contract, not by copying product logic."**
No such contract exists yet.

The first candidate module is **Creative OS**, built in
[`Ceoloo/aion-creative-os-CVD`](https://github.com/Ceoloo/aion-creative-os-CVD):
a Next.js app, a Node media worker and its own Supabase project (Auth, Storage,
Postgres with RLS). Its Phase 1 (media intake with a leased, fenced job queue)
is implemented and tested locally; transcription, clip finding, approval and
rendering are later phases. Its own rules already require human approval bound
to an exact edit-plan revision/hash before rendering.

Facts that constrain the decision:

1. **Runtime is the sole execution, policy, approval and audit authority**
   ([ADR-002](ADR-002-runtime-host-ownership.md),
   [ADR-005](ADR-005-runtime-identity-plane.md)). Approvers are derived from the
   authenticated principal and must be registered human actors with the
   `approve` role in the request's tenant.
2. **AION Data excludes product entities.** "No CRM, sales, content, or portal
   tables"; products reach it through Runtime HTTP, not by importing
   `@aion/data`.
3. **External systems are reached through Runtime adapters** with backend
   policy, a fake backend for tests, production write eligibility and an
   external side-effect ledger (the GHL adapter is the reference).
4. **The precedent to avoid:** the AION-Sys revenue copilot runs its own
   Supabase schema and does not go through the Execution Gateway
   ([repositories/README.md](../repositories/README.md)). A product module with
   a parallel approval loop would repeat that.
5. **First external customer:** a client of AION's services (done-for-you),
   not self-serve signup. The client will approve outputs with **their own
   approver principal**.
6. **Current priority:** GE-001 certification and the Pilot-to-Revenue Program
   ([offers/aion-pilot-to-revenue-program.md](../offers/aion-pilot-to-revenue-program.md)).
   This ADR must not displace them.

## Decision

We will integrate Workspace product modules through one **Product Module
Contract** ([`architecture/product-module-contract.md`](../architecture/product-module-contract.md)),
and Creative OS will be the first module built against it.

1. **Repository status.** `aion-creative-os-CVD` is a **product-module
   satellite** outside the six canonical platform repositories, like
   `aion-desks` ([ADR-004](ADR-004-aion-desks-repo-ownership.md)) but
   Runtime-integrated. It owns creative product logic and product data only.
   It does not import `@aion/core` or `@aion/data` and does not host execution
   policy.
2. **Two systems of record, split by kind.** The module's store holds product
   data (for Creative OS: media, transcripts, edit plans, content captures,
   renders). AION Data, through Runtime, holds governance truth: missions,
   executions, approvals, side effects, outcomes, audit. Neither copies the
   other's canonical records; each stores the other's identifiers as
   references.
3. **Governed actions go through Runtime.** Module capabilities are registered
   as Service Catalog entries (`serviceKey` `name@version`) with risk level and
   action tier. Runtime reaches the module through a **module adapter** that
   follows the GHL adapter pattern and records external side effects. The
   Workspace never calls module write APIs to perform governed actions
   directly.
4. **Approvals have two checks, one inbox.** Runtime owns the human gate,
   approver identity and audit. The module owns domain validity: the gate's
   proposal carries the module's revision id and content hash, and the module
   executes only when its persisted revision/hash **and** the Runtime approval
   id match. An edit after approval invalidates both. Approvals are decided in
   the Workspace Approvals app, never inside the module UI.
5. **Clients approve as themselves.** Each client approver gets a Runtime
   principal bound to a registered human actor with `approve`, scoped to the
   client's tenant ([ADR-005](ADR-005-runtime-identity-plane.md)). Operators do
   not approve on a client's behalf for client-gated actions.
6. **Tenancy.** Module data is scoped by the Runtime tenant id. The module
   never accepts tenant or actor identity from the browser.
7. **Embedding.** A module opens in the Workspace as its own web app in a
   window (`kind: remote_module`), served from a subdomain of the same parent
   domain as the Workspace, with a server-side SSO handoff, a framing lock and
   a small window message protocol. It also works standalone in its own tab.
8. **Honest availability.** A module stays `planned` in the app registry until
   its first service passes acceptance evidence. `connected` and `available`
   are earned, not declared.

The required capabilities for the SSO assertion, adapter status reporting and
module data store are stated in the contract. **No platform-wide vendor is
selected here** (capability-over-vendor rule). Creative OS's use of Supabase is
a product-internal choice inside its boundary, not a platform standard.

## Alternatives Considered

- **Alternative A: Fold Creative OS into `aion-products`.** One product
  monorepo. **Deferred, not chosen now:** Creative OS has its own stack
  (media worker, FFmpeg, its own Supabase and storage) and phase plan; moving
  it now costs a migration without changing the governance boundary. Revisit
  with a new ADR if a single product monorepo is funded (same reasoning as
  ADR-004 Alternative B).
- **Alternative B: Rebuild Creative OS screens natively inside the
  Workspace.** **Rejected:** copies product logic into the shell, contradicting
  the blueprint's "module contract, not copied logic" and coupling Workspace
  releases to media features.
- **Alternative C: Let the module keep its own approval and execution loop.**
  **Rejected:** creates a second control plane, the defect already flagged for
  the AION-Sys revenue copilot.
- **Alternative D: Store creative entities in AION Data.** **Rejected:** AION
  Data explicitly excludes product/content tables; large media and product
  schemas belong to the product.
- **Alternative E: Open modules only as external links, no window
  embedding.** **Kept as fallback only:** links work without SSO but break the
  single-surface experience the Workspace exists to provide.
- **Alternative F: Operators approve on clients' behalf, quoting client
  consent in the note.** **Rejected:** attribution becomes asserted rather than
  authenticated, which ADR-005 and the approval-identity rules discourage.

## Consequences

### Positive

- Every product module (Creative OS, then Wealth OS, Music Label OS and others)
  integrates the same way. The Workspace, Runtime and module teams work from
  one contract.
- No second control plane: approvals, audit and outcomes for creative work land
  in the same Missions/Approvals surfaces as revenue work.
- AION Data stays free of product schemas; product teams keep ownership of
  their data and release pace.
- Client approvals are authenticated and attributable.

### Negative

- Two systems of record must be reconciled by reference ids; drift is possible
  and needs checks in acceptance tests.
- Runtime gains one adapter per module, plus catalog entries and proofs.
- The dual approval check (Runtime approval + module hash) adds implementation
  and test burden in both places.
- Window embedding needs a shared parent domain (not yet purchased), SSO
  signing keys and framing rules, which adds infrastructure work before the
  first connected module.
- Client principals add identity administration (provisioning, rotation,
  revocation) per client.
- One more satellite repository in the workspace map.

## Implementation Notes

Merge order follows [`.aion/standards/git.md`](../.aion/standards/git.md):
contract docs first, then Runtime, then product/frontend.

| Repository | Change | When |
|---|---|---|
| `aion-docs` | This ADR, the contract doc, the satellite page, index and map rows | Now |
| `aion-creative-os-CVD` | Record this ADR in `docs/ecosystem.md`; add `tenant_id` scoping and Runtime approval-id binding with the approval phase (its Phase 4); a server API for the adapter; SSO exchange; framing lock | With its Phase 4–5 work |
| `aion-runtime` | `creative` adapter (fake backend first), catalog entries, proof matrix in the operator-loop evidence format | After Creative OS Phase 5 exists |
| `aion-data` | None expected: executions, approvals, side effects and outcomes already cover it. Any new need goes through a migration and its own review | — |
| `aion-command-center` | `creative-os` manifest (stays `planned`), SSO assertion issuer, window message protocol, module artifacts in Missions | After OS-004 |
| `aion-infra` | Parent domain, subdomains, TLS, signing-key storage | When the domain is purchased |

Sequencing guard: GE-001 and the Pilot-to-Revenue Program keep priority.
Creative OS integration work starts only after Creative OS completes its own
Phases 2–5 and Workspace OS-004 lands.

## Follow-up Decisions

- **Creative OS mission charter.** Products trace to a mission
  ([missions/lifecycle.md](../missions/lifecycle.md)); the Creative OS mission
  (first client of AION's services) must be defined before integration build.
- **Adapter status reporting.** Polling vs callback from module to Runtime:
  capability stated in the contract; mechanism chosen during the first adapter
  build, by ADR only if it changes a cross-repo contract.
- **SSO assertion format and key management.** Capability stated; technology
  deferred.
- **Workspace repository status.** `aion-command-center` is not yet listed in
  the repository maps; its canon/satellite status needs recording (relates to
  ADR-013).
- **Desktop modules.** ColorGrade and other local tools as `local_native` apps
  via the planned Native Companion (V2): separate decision when that program
  starts.
- **Consumer front end.** IclipU (self-serve consumer clipper on Creative OS
  services) depends on Workspace customer identity; separate decision.
