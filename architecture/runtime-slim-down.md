# Runtime Slim-Down Plan

- **Status:** Active architecture plan
- **Binding ADR:** [ADR-008](../adr/ADR-008-runtime-composition-scope.md)
- **Also references:** [ADR-002](../adr/ADR-002-runtime-host-ownership.md),
  [ADR-003](../adr/ADR-003-execution-gateway-into-runtime.md)

## Goal

Keep `aion-runtime` a **composition root + thin Execution Gateway**, not an
application monolith. Preserve a single `/v1` ingress (ADR-003) while making
domain bulk extractable and reviewable.

## Current problem

| Signal | Why it matters |
|---|---|
| Very large gateway module | Ingress and domain workflows share one file → every product feature edits Runtime |
| CRM / GHL adapters co-located | Platform host owns vendor integration depth |
| Mission capability/risk tables in host wiring | Orchestration *policy config* leaking out of Core |
| Runtime→`DataLayer` field access | Bypasses Core ports → incomplete substitutability |
| Vendor pin + tree overlays | Release integrity coupled to scripted surgery |

## Target shape

```text
aion-runtime
├── boot / config / health / migrate          # composition root (ADR-002)
├── http/
│   └── gateway routes (thin)                 # parse → authorize context → call app service
├── app/                                      # use-case services (temporary home)
│   ├── commands/
│   ├── missions/
│   ├── economics/
│   ├── evaluations/
│   ├── autonomy/
│   ├── implementations/                      # IE — extract or productize later
│   └── sessions-outcomes/
└── adapters/
    └── ghl/                                  # external side-effect adapter (ledgered)
```

**Rule:** route handlers do not contain business branching beyond HTTP mapping.
Use-case services call **Core** for governed decisions and **Data ports** for
persistence. New Core ports are preferred over new `dataLayer.foo` reaches.

## Phased extraction (no big-bang)

### Phase A — Modularize in place (Runtime stays owner)

1. Split gateway route table from handlers without behavior change.
2. Group handlers by use-case folder; add characterization tests from existing
   proof matrices.
3. Replace ad-hoc `DataLayer` reaches with Core ports where ports already exist;
   file port gaps in contract-hygiene.

### Phase B — Adapter boundary

1. Define a narrow interface for CRM side effects (read/write + idempotency keys)
   aligned with `ExternalSideEffect` in Core.
2. Keep GHL as one implementation behind that interface inside Runtime image.
3. Forbid new vendor SDKs in Runtime (infra/provider profiles remain elsewhere).

### Phase C — Policy config repatriation

1. Move mission capability/risk seed maps into Core catalog/policy modules (or
   Data-seeded catalog loaded by Core) so Runtime only loads configuration.
2. Runtime retains environment overlays (env-specific allowlists), not domain
   defaults.

### Phase D — Product/IE demotion check

1. Apply the [platformization rule](../roadmap/platform-maturity.md#platformization-rule)
   to Implementation Engine and revenue-session façades.
2. If not platform-worthy, relocate to `aion-products` as Runtime **clients**.
3. If platform-worthy, promote contracts into Core and keep a thin Runtime host
   path.

## Done when

- Gateway entry file is primarily routing + middleware.
- No new product feature *requires* editing Runtime except for a new route that
  delegates to an existing use-case or Core command.
- Core ports cover durable concerns Runtime mutates in production paths.
- Pins are tagged releases; tree-surgery overlays are gone
  ([contract-hygiene](../engineering/contract-hygiene.md)).

## Non-goals

- Creating a second HTTP gateway service.
- Rewriting Mission proof harnesses in the same change as extraction.
- Moving Postgres schema ownership out of `aion-data`.
