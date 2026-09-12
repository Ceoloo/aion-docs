# GHL Phase A (read-only) → Phase B (governed write)

**Immediate milestone (landed):** AION can read a tenant’s GHL state, propose one
CRM mutation, route it through the Execution Gateway, require approval when
policy demands it, execute it once, and record the result.

| Plane | PR | Status |
|---|---|---|
| Catalog / confidence gate | aion-core #17 | ✅ **Landed** |
| Adapter + proof matrix | aion-runtime #24 | ✅ **Landed** |
| Live acceptance harness | aion-runtime #25 → #27 (main) | ✅ **Landed** + prod gate green |
| Roadmap | aion-docs #33 | ✅ **Landed** |
| Empire flywheel doctrine | aion-docs #34 | ✅ **Landed** |
| Next-priority record | aion-docs #35 | ✅ **Landed** |

This is **not** a second runtime. It extends Mission 009’s GHL adapter +
`ExternalSideEffect` ledger.

**Integration priority:** prove **one governed GHL write end-to-end against a
real tenant**. That is the only active engineering objective for this slice.

### Standing rules

| Rule | Status |
|---|---|
| IE-002 | **Closed** — do not reopen |
| OL-001 | **Unpaused for Console Production launches** — live GHL verified; live model still blocked in Cloud Agent harness; **M001 ModernRelx launched** (`msn_648d3df8-…`, `productionEconomic=true`) |
| OL-001 M001 next | Merge/deploy runtime #34 ✅ merged + image published; **operator deploy required**; one `crm.task.create` retry; pass or **visible waiver** (`completed_with_exception`) then **M002** — do not over-engineer task.create |
| OL-001 resume | Production mode is live; keep adding OpenRouter / Revenue Copilot so model-backed proofs green |
| New substrate | **No** — unless this Phase A→B proof exposes an actual blocker |

### Live capability matrix (2026-09-08, prod `3329c01…`)

| Gate | Required | Result |
|---|---|---|
| Live GHL tenant read (normalized + evidence) | ✅ | ✅ contacts/opps/pipelines/conversations/appointments via `ghl-live` |
| Live model call (structured classify) | ✅ | ❌ `OPENROUTER_API_KEY` unset; Revenue Copilot profile not deployed/reachable |
| Proposed CRM mutation | ✅ | ✅ note body prepared (model_pending until model lands) |
| R2 approval required | ✅ | ✅ `crm.opportunity.update` → `REQUIRE_APPROVAL` `apr_0e58ad06-…` |
| Human approval recorded | ✅ | ✅ |
| One real GHL write | ✅ | ✅ `crm.note.create` → note `i8vqiqM5AlmtJSNJcZDp` / `ese_0e792897-…` |
| Replay creates no duplicate | ✅ | ✅ same idempotency key → `idempotentReplay: true` |
| Provider response captured | ✅ | ✅ |
| Full AION audit | ✅ | ✅ |

Harness: `npm run proof:ghl-live-capability` (aion-runtime).  
**OL-001 Production mode is enabled** and **Mission 001 / 100 (ModernRelx)** is live. Install OpenRouter on the host for Revenue Copilot — do not paste keys into chat — then re-run the harness with `OPENROUTER_API_KEY` in the proof environment or against a live Copilot so model-backed steps green. Do not count PRE-OL / infra proofs as OL-001 credit.

Note: `crm.note.create` is **R1 ALLOW** (gateway-governed, no approval). The matrix R2 row is satisfied by `crm.opportunity.update` in the same proof run.

### Acceptance milestone (active gate)

```text
real tenant read
  → CRM change proposed
  → explicit human approval
  → execute exactly once
  → full AION audit
```

Fake-backend CI proof is necessary but **not** sufficient. This gate is the
shortest path from current infrastructure to the first trustworthy live revenue
workflow.

### Audit minimum (must be captured)

| Field | Why |
|---|---|
| `tenant` | Isolation / attribution |
| `operator` / `approver` | Human accountability |
| `source read` | What CRM state justified the proposal |
| `proposed mutation` | Exact change requested |
| `policy decision` | ALLOW / DENY / REQUIRE_APPROVAL (+ reason) |
| `approval` | Decision record (who / when / note) |
| `execution id` | Durable Execution Object link |
| `idempotency key` | Exactly-once guarantee |
| `GHL response` | Vendor result / error body (redact secrets) |
| `timestamp` | When it happened |
| `success` / `failure` | Terminal outcome |
| `cost` | Execution / side-effect cost record |

Missing any of the above = gate **not** met.

---

## Phase A — READ

```text
GHL auth/config (env)
  → tenant-scoped connection (location binding)
  → contacts (read/search)
  → opportunities (read/search)
  → pipelines / stages
  → conversations
  → appointments (read)
  → normalize into Crm* adapter contract
  → prove tenant isolation + observability
```

| Capability | Risk | Notes |
|---|---|---|
| `crm.contact.read` / `search` | R1 | Active |
| `crm.opportunity.read` / `search` | R1 | Active |
| `crm.pipeline.read` | R1 | Active |
| `crm.conversation.read` | R1 | Active |
| `crm.appointment.read` | R1 | Active (writes still inactive) |

**Backends**

- `FakeGhlBackend` — seeded fixtures for CI / proofs
- `LiveGhlBackend` — selected when `GHL_API_KEY` + `GHL_LOCATION_ID` are set
  (`createGhlBackendFromEnv`)

**Connection:** Phase A uses env binding (`resolveGhlConnection`). Cross-location
overrides are refused. Multi-tenant connection store can replace this later
without changing the adapter port.

**Proof:** `npm run proof:ghl-phase-ab` (A1–A4) — green on fake backend in CI.

---

## Phase B — GOVERNED WRITE

```text
AION mission / agent
  → proposed CRM mutation
  → Execution Gateway
       identity · tenant · permission · risk · approval · idempotency · audit
  → human approval when required
  → GHL adapter write
  → ExternalSideEffect + execution record
```

### First write surface (small)

| Mutation | Risk | Approval | Notes |
|---|---|---|---|
| `crm.note.create` | R1 | no | Safe first write |
| `crm.task.create` | R1 | no | Safe first write |
| `crm.opportunity.update` (stage) | R2 | **yes** | Milestone approval proof |
| `crm.contact.update` upsert | R2 | yes | Requires `matchConfidence ≥ 0.85` |
| `crm.message.send` | R3 | yes | **Deferred** — higher customer impact |

Do **not** start with outbound messaging or broad contact mutation.

**Proof:** `npm run proof:ghl-phase-ab` (B1–B5):

1. Propose opportunity stage update → `REQUIRE_APPROVAL`
2. Human approve → execute once → `sideEffectId`
3. note + task through gateway
4. Audit via execution / side-effect ledger
5. Low-confidence upsert denied

---

## Next integration slice (follow-up — not IE-002)

**Done when** the active acceptance gate above is green on a **real** tenant,
including the audit minimum.

Ordered work:

1. ~~**Operator keys / scopes**~~ — CRM scopes verified on AION Empire; **still** install rotated PIT on VPS `/opt/aion/.env` (`0600`); verify live model access separately.
2. ~~**Live Phase A**~~ — contacts / pipelines / opportunities / conversations read via `ghl-live` backend (local Runtime proof 2026-09-07).
3. ~~**Live Phase B (local Runtime)**~~ — opportunity stage propose → approve → execute once → audit → restore (aion-runtime #25).
4. **Production Runtime install** — VPS env + infra #7 deploy; re-prove on `runtime.srv…` / Console approval path.
5. **Hardening only if the proof exposes a blocker** — no speculative substrate; `crm.message.send` still deferred.

Explicitly **out of this follow-up:** reopening IE-002, appointment writes,
autonomous messaging, starting the OL-001 100-mission cohort.

---

## Operator standing items

1. Rotate GHL + OpenRouter keys into `/opt/aion/.env` (`0600`) on the Runtime host
2. For live reads/writes: set `GHL_API_KEY`, `GHL_LOCATION_ID`, `GHL_API_VERSION`
3. Ensure VPS compose passes `GHL_*` into `aion-runtime` (aion-infra #7)
4. Keep `crm.message.send` behind stricter product policy even after keys exist

### Live credential + acceptance evidence (2026-09-07)

| Check | Result |
|---|---|
| Location ID `YK8RT5OnmQiMqprlyqYY` | ✅ **AION Empire** |
| Contacts / opportunities / pipelines / conversations | ✅ CRM scopes green |
| Calendars | ✅ list empty; appointment reads return empty list |
| `npm run proof:ghl-live-acceptance` | ✅ green on local Runtime + live GHL |

| Audit field | Evidence |
|---|---|
| tenant | `aion-systems` |
| source read | contact `MyWCgeFaKnifp6LM7yIc`; opp `rGbIyrAvGDcmMEzjBER4` (Negotiation) |
| proposed mutation | stage → Proposal Sent |
| policy | `REQUIRE_APPROVAL` (R2) |
| approval | `apr_509b038b-…` |
| execution / side-effect | `exe_86da771d-…` / `ese_e613a428-…` succeeded |
| idempotency | `ghl-live-stage-rGbIyrAvGDcmMEzjBER4-…` |
| GHL outcome | stage applied; restored to Negotiation after proof |
| success | ✅ |

**Production Runtime status:**

| Item | Status |
|---|---|
| PIT scopes + `/opt/aion/.env` (infra + `GHL_*`) | ✅ |
| aion-infra #7 compose `GHL_*` | ✅ merged + surgical host apply |
| Runtime image | ✅ `3329c01b05dc4e0be60231262166d6aa636e9ccf` (main #27–#30) |
| Live acceptance on `runtime.srv1655818.hstgr.cloud` | ✅ **green** 2026-09-08 |

### Production gate evidence (2026-09-08)

| Audit field | Evidence |
|---|---|
| prod `git_sha` | `cdb622959817c1bff6a74e10a5c14b7a5e9bedc7` |
| backend | `ghl-live` |
| tenant / location | `aion-systems` / `YK8RT5OnmQiMqprlyqYY` |
| source read | contact `MyWCgeFaKnifp6LM7yIc`; opp `rGbIyrAvGDcmMEzjBER4` (Negotiation) |
| proposed mutation | stage → Proposal Sent |
| policy | `REQUIRE_APPROVAL` (R2) `apr_ee0beb88-…` |
| execution / side-effect | `exe_03cd4efe-…` / `ese_bb8cc26b-…` succeeded |
| idempotency | `ghl-prod-stage-rGbIyrAvGDcmMEzjBER4-…` |
| cost | `{ units: 4, tokens: 60 }` |
| GHL outcome | stage applied; restored to Negotiation |
| success | ✅ |

**Catalog follow-up:** ✅ closed via aion-runtime #30 (`AION_DATA_REF` bump → `seedMission009` 15 caps) on prod `3329c01…`. Full `npm` harness `ghl-live-acceptance` green on prod (L1–L8): `exe_11e4d033-…` / `ese_40620db2-…` / `apr_bd2cd0aa-…`.

---

## Explicit non-goals (this slice)

- Continuous live-call streaming
- Appointment **writes** / calendar booking automation
- Autonomous customer messaging
- Counting the live proof as OL-001 mission credit (M001 is a separate production launch; proofs still do not count toward 100 until
  live GHL **and** live model access are both verified, then starts its own
  cohort)
- Reopening IE-002 provisioning/activation work
- New substrate without a Phase A→B proof blocker

## AIO-17 lead-workflow adapter slice (Runtime)

First adapter slice lands in `aion-runtime` (`docs/ghl-aio17-lead-workflow.md`):

- Enabled: contact get/upsert, opportunity create/stage update, note create, task create
- Disabled (explicit `CAPABILITY_DISABLED`): conversation read/send, appointment create
- Pinned API version: `2021-07-28`
- Proof: `npm run proof:aio17-ghl-lead-workflow`

AIO-17 remains partially complete; OL-001 broader resume still requires remaining
capabilities and the model-provider dependency.
