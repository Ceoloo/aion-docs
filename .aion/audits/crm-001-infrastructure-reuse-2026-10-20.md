# CRM-001 — AION GitHub infrastructure reuse audit

- **Date:** 2026-10-20
- **Mission:** [CRM-001](../missions/CRM-001-twenty-integration-benchmark.md)
- **Doctrine:** [ADR-019](../../adr/ADR-019-crm-soe-ownership-path.md) (Accepted — Twenty primary SoE)
- **Purpose:** Establish what we can **reuse** before creating any additional
  services for AION CRM Sovereign Infrastructure V1.

---

## Executive verdict

**Do not create a new CRM microservice or greenfield CRM schema yet.**

AION already has the governed CRM *plane*: provider-neutral `crm.*`
capabilities, a Runtime adapter + fake/live backend port, an idempotent
external side-effect ledger, approval/risk gates, and VPS deploy patterns
(Traefik + internal Docker network + `/opt/aion/.env`). The missing work is a
**Twenty backend** (and EspoCRM benchmark harness) behind that plane — not a
parallel stack.

| Layer | Reuse? | Note |
|---|---|---|
| `crm.*` catalog + RCAOS process | **Yes** | Keep; extend for `company` / activity if needed |
| Runtime GHL adapter pattern | **Yes → extract** | Rename port toward `CrmBackend`; keep GHL as migration adapter |
| `external_side_effects` | **Yes** | Already `provider` column (default `ghl`); add `twenty` / `espo` |
| aion-data customer entity tables | **No today** | Explicitly excluded; SoT decision needs a data-layer follow-up |
| VPS compose / Traefik / internal net | **Yes** | Template for private Twenty + EspoCRM |
| Agents → CRM DB direct | **Forbidden** | Products already route via Runtime `crm.*` |
| kargulstudio/sales-crm etc. | **Reject** | Not a CRM |

---

## 1. REUSE AS-IS

### Core — capability catalog & process contracts

| Asset | Path | Reuse |
|---|---|---|
| Mission 009 / AIO-17 service keys | `aion-core` `src/contracts/service-catalog.ts` (`MISSION_009_SERVICE_KEYS`) | Contacts, opportunities, pipeline, notes, tasks, messages, appointments (read) |
| Secure Automation lead workflow | `aion-core` `src/contracts/secure-automation.ts` | Lead → qualify → opportunity → draft → human book |
| Contact upsert confidence gate | `CRM_CONTACT_UPSERT_MIN_CONFIDENCE` (0.85) | Keep for Twenty writes |
| External side-effect types | `@aion/core` `ExternalSideEffect` / idempotency helpers | Provider-agnostic ledger contract |

**Gap vs stated V1 entities:** catalog has **contact** and **opportunity**, not
a first-class `crm.company.*` or `crm.activity.*`. Notes/tasks cover part of
“activities.” CRM-001 adapter work should map Twenty Company → either new
catalog keys or enrich-on-contact — **decide before inventing services**.

### Runtime — adapter plane

| Asset | Path | Reuse |
|---|---|---|
| Backend port shape | `aion-runtime` `src/adapters/ghl/types.ts` (`GhlBackend`) | Same `execute({ tenantId, action, payload, idempotencyKey })` for Twenty |
| Fake backend | `fake-ghl-backend.ts` | Pattern for `fake-twenty-backend` / shared fake CRM |
| Live backend + connection | `live-ghl-backend.ts`, `connection.ts` | Pattern for Twenty REST/GraphQL client |
| Adapter + ledger | `ghl-adapter.ts` | Policy-after-ALLOW, side-effect record, ambiguous-write handling |
| Backend selection policy | `backend-policy.ts` | Extend: `CRM_PROVIDER=twenty\|ghl\|fake` (do not silently default live) |
| Production write eligibility | `production-write-eligibility.ts` | Protect live records; extend for Twenty fixture namespaces |
| Payload validation | `payload-validation.ts` | Provider-specific validators behind shared actions |
| Normalize | `normalize.ts` | Provider → AION-normalized contact/opp shapes |

### Data — governance SoR (not CRM CRUD)

| Asset | Path | Reuse |
|---|---|---|
| External side-effect ledger | migration `0008_external_side_effects.sql`; `PostgresExternalSideEffectRepository` | Idempotent CRM mutations; `provider` already exists |
| Executions / approvals / outcomes | existing aion-data schema | Workflow verification spine |
| Mission 009 catalog seed | `postgres-service-repository` seed | Reuse for Twenty-capable keys |

**Standing exclusion (must resolve for “AION Data = customer SoT”):**

> `aion-data` README: *no CRM, sales, content, or portal tables.*  
> `docs/schema.md`: *GHL owns CRM state; AION owns governance truth.*

That conflicts with the V1 table “Source of truth = AION Data — canonical
customer and revenue records.” **CRM-001 must not invent customer tables until
a follow-up data-layer ADR amends this boundary** (options: sync projection /
identity map only vs full customer SoR in Postgres). Until then, treat Twenty
as operational SoE and aion-data as governance + outcome + external id map via
`external_side_effects.external_resource_id`.

### Infra — deploy & secrets

| Asset | Path | Reuse |
|---|---|---|
| VPS compose | `aion-infra` `providers/vps/docker-compose.yml` | Traefik labels, `internal` + `traefik` networks, non-published ports |
| Local Postgres Mode A | same compose + deploy scripts | Pattern for Twenty’s DB (separate volume/DB — do not share `aion_data`) |
| Secrets file | `/opt/aion/.env` 0600 pattern; `GHL_*` allowlist | Mirror as `TWENTY_*` / `ESPO_*` — never `VITE_*` |
| Health / ready | runtime `/health/ready` + `crm_backend` reporting | Extend for CRM provider |
| Credential design | `aion-infra/docs/design/external-credentials.md` | Same secret-store rules |

### Products / Workspace

| Asset | Path | Reuse |
|---|---|---|
| Workforce Control workflows | `aion-products` `workforce-control` (`crm.contact.*`, `crm.opportunity.*`) | Already Runtime-capability oriented |
| RCAOS / lead workflow composition | docs + `@aion/core` secure-automation | First real workflow target |
| Operator Console / AION OS shell | command-center / desks patterns | Embed Twenty UI later as module/shortcut — **not** in CRM-001 critical path |

### Docs / proofs

| Asset | Path | Reuse |
|---|---|---|
| GHL capability matrix | `offers/revenue-capture-appointment-os/ghl-capabilities.md` | Rename/generalize to CRM capabilities scorecard |
| Phase A→B roadmap | `roadmap/ghl-readonly-governed-write.md` | Acceptance pattern for live write |
| Proof scripts | `aion-runtime` `proof:mission009`, `proof:aio17-ghl-lead-workflow`, `proof:rcaos-at`, `proof:ghl-live-capability` | Clone matrix for Twenty fake → live |
| REG / sovereignty | ADR-016, ADR-018, SOV-003 | Twenty on **internal** Docker network may be same-host (not public egress); still classify payloads |

---

## 2. REUSE WITH EXTRACTION (do before new services)

Ordered engineering slices — **no new repos**:

1. **Extract provider-neutral port** in `aion-runtime`:
   - `GhlBackend` → `CrmBackend` (or keep GHL types as alias).
   - `GhlMutationKind` → shared CRM actions; provider mappers underneath.
2. **Provider selection**: `CRM_PROVIDER` / backend policy supporting
   `fake | ghl | twenty` (Espo only in benchmark harness, not production path).
3. **Allowlist REG**: add `twenty` (and document private-network = non-public);
   keep `ghl` during staged migration.
4. **Catalog hygiene**: descriptions that say “GHL” → “tenant CRM workspace”;
   add `crm.company.*` only if Twenty Company is in V1 scope.
5. **Ledger**: write `provider: 'twenty'` on side effects; dual-run proofs.

---

## 3. DO NOT REUSE / REPLACE LATER

| Item | Why |
|---|---|
| Live GHL as long-term primary SoE | Superseded by ADR-019; keep as migration adapter only |
| Agents or products calling CRM SQL/HTTP directly | Violates Runtime ownership + REG |
| Sharing `aion_data` Postgres with Twenty | Blast radius; separate DB/volume |
| Boilerplate “sales-crm” starters | Not CRMs |
| Building thin AION CRM UI in CRM-001 | Explicitly out of scope (Twenty is the operational UI) |

---

## 4. GAPS (create only these)

| Gap | Minimum artifact | New service? |
|---|---|---|
| Self-hosted Twenty (dev) | Compose service(s) on `internal` network + isolated creds | **No** — add to `aion-infra` profile / overlay |
| EspoCRM benchmark harness | Disposable compose + metrics script (mem/CPU/API latency/deploy steps) | **No** — script + ephemeral stack |
| Twenty Runtime backend | `live-twenty-backend.ts` (+ tests) | **No** — module under `src/adapters/` |
| Company / activity mapping | Catalog decision + mapper | Only if keys missing |
| Canonical customer SoR in aion-data | Follow-up ADR + migrations | **Not in CRM-001 deploy** until ADR |
| GHL → Twenty staged migration | Reconcile tooling + rollback | Scripts; no new microservice |
| Offline resilience drill | Local Twenty + Runtime + Postgres without public net | Ops runbook |

---

## 5. Recommended CRM-001 sequence (reuse-first)

Matches leadership sequence; annotated with reuse:

1. **Inspect** — this audit (done as docs artifact).
2. **Deploy Twenty (dev)** — copy VPS compose patterns; private network; own DB.
3. **Benchmark EspoCRM** — same host class; record metrics; no production adapter
   required.
4. **AION CRM adapter** — extract `CrmBackend`; implement Twenty; fake proofs
   first (`proof:rcaos-at` parity).
5. **Agents via Runtime only** — verify workforce-control / copilot never get
   Twenty DB URLs.
6. **First real workflow** — lead → qualify → opportunity → approved action →
   Outcome (fake Twenty, then live Twenty).
7. **Staged GHL migration** — dual provider; reconcile; client isolation;
   rollback to GHL adapter.

---

## 6. What not to build yet

- New GitHub repo for “aion-crm”
- Customer/opportunity tables in `aion_data` (pending data-layer ADR)
- SMS/email provider replacement (GHL channel parity is a later milestone)
- Production cutover or DNS for client Twenty workspaces

---

## Handoff

```text
AUDIT                    CRM-001 infrastructure reuse (2026-10-20)
VERDICT                  Reuse Runtime crm.* plane + ledger + VPS compose; do not create new CRM services
BLOCKER                  aion-data “no CRM tables” vs V1 “AION Data = customer SoT” — needs follow-up ADR before entity migrations
NEXT                     CRM-001 mission: Twenty compose overlay + CrmBackend extraction spike
```
