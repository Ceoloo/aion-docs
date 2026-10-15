# aion-creative-os (Creative OS)

**Repository:** [`Ceoloo/aion-creative-os-CVD`](https://github.com/Ceoloo/aion-creative-os-CVD)
**Role:** Product-module satellite: AI-assisted creative production for AION
clients, opened inside the AION Workspace. Status per
[ADR-015](../adr/ADR-015-workspace-product-module-contract.md) (**Proposed**).

## Owns

- Creative product UI and logic: media intake, transcription, clip proposals,
  edit plans, rendering; later content capture and campaign packs.
- Its product data store and media storage (product-internal choice; currently
  Supabase).
- Its workers (media processing, rendering).

## Does NOT own

- Approval authority, identity, tenancy decisions, audit (Runtime).
- Governance records (AION Data).
- Control-plane, canonical schema or gateway concerns.

## Integration

Follows the [Product Module Contract](../architecture/product-module-contract.md):
Service Catalog entries, a Runtime module adapter, Runtime-owned approvals with
module revision/hash binding, tenant-scoped data, and a Workspace window with
SSO handoff.

## Dependency rule

May call Runtime-facing module APIs and its own providers. Must not import
`@aion/core` or `@aion/data`, write to AION Data, or host execution policy.

## Status

Phase 1 (media intake, leased job queue) implemented and locally tested.
Transcription, clip finding, approval and rendering are later phases. Listed as
`planned` in the Workspace until contract acceptance evidence passes for every
advertised gated service. Sequencing and gates:
[roadmap/aion-build-map-creative-os.md](../roadmap/aion-build-map-creative-os.md).
