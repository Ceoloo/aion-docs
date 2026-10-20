# SOV-001 — Sovereignty Inventory (2026-10-19)

> **Mission:** [SOV-001](../missions/SOV-001-sovereignty-audit.md)  
> **Doctrine:** [architecture/sovereign-intelligence-doctrine.md](../../architecture/sovereign-intelligence-doctrine.md)  
> **ADR:** [ADR-016](../../adr/ADR-016-sovereign-intelligence-doctrine.md)  
> **Program:** [roadmap/sovereignty-program.md](../../roadmap/sovereignty-program.md)  
> **Scope:** Code + documented ops across `aion-core`, `aion-data`, `aion-runtime`, `aion-infra`, `aion-products`, `aion-desks`, `aion-docs`, and AI tooling paths.  
> **Constraint:** Inventory and classify only. No production cutovers. Secret *names* only — never values.

## Classification legend

| Label | Meaning |
|---|---|
| **compliant** | Meets doctrine today (AION-controlled path; no unauthorized IP egress for proprietary work). |
| **migrate** | Must move to AION-controlled / local / self-hosted path; not shutdown today. |
| **optional-external** | Allowed as authorized external for non-proprietary or SoE roles; never SoR for protected intelligence; gateway + policy required. |
| **exception-ADR-required** | Conflicts with doctrine or prior Accepted ADR; needs explicit time-bounded exception or supersession ADR. |

---

## Executive verdict

AION’s **control-plane kernel is already largely sovereign by design** (`aion-core` has no outbound SDKs; `aion-data` is vendor-neutral Postgres; Runtime SoR on VPS is **local Postgres**, not Supabase).

The **material sovereignty gaps** are concentrated in:

1. **Public frontier inference for proprietary work** — Anthropic / OpenRouter from `aion-products` (and OpenRouter in Runtime proof scripts) send live conversation/business context off-boundary.
2. **Legacy Supabase project** — still backed up and recoverable (`AION EMPIRE SYSTEM`); not Runtime SoR, but remains a hosted critical-data island.
3. **Hosted delivery** — Vercel for Operator Console / Revenue Copilot UI / Desks; temporary per doctrine, no complete self-hosted UI path proven as primary.
4. **CRM SoE** — GHL remains live migration adapter (authorized external egress); **Twenty** is the ADR-019 primary SoE (self-hosted, same-host preferred). Neither is intelligence SoR; Runtime + REG required.
5. **Missing local inference / offline queues / owned RAG** — not built; Phase 2–4 work.
6. **AI development tooling** — Cursor / Claude / GitHub hold architecture and source; access restriction policy incomplete vs doctrine § stack table.
7. **ADR-010 PostHog** — Accepted hosted analytics plane; conflicts with sovereignty until self-host / EU / exception ADR.

**Do not shut anything down from this audit.** Use the Phase 2 backlog below.

---

## 1. Master inventory (cross-repo)

| ID | Component | Kind | Repos / locus | Proprietary data risk | Class | Notes |
|---|---|---|---|---|---|---|
| S-01 | `aion-core` control plane | First-party kernel | `aion-core` | None outbound | **compliant** | zod only; ports only; no provider SDKs |
| S-02 | Postgres (`aion_data`) on Hostinger VPS | Owned persistence | `aion-data`, `aion-runtime`, `aion-infra` | Critical (SoR) | **compliant** | Local Docker Postgres Mode A; AION roles `aion_app` / `aion_migrator` |
| S-03 | Supabase project `qbahthzqvxytfgobgtxa` (“AION EMPIRE SYSTEM”) | Hosted Postgres island | `aion-infra` recovery docs | High | **migrate** | Nightly B2 backup; restore-tested; **not** Runtime SoR; free plan = no platform backups |
| S-04 | GoHighLevel / LeadConnector API | CRM SoE (migration) | `aion-runtime` adapters; infra env | Critical (PII/CRM) | **optional-external** | ADR-019: demoted from primary; keep for dual-run / rollback; live when `GHL_BACKEND=live`; REG required |
| S-04b | Twenty CRM (self-hosted) | CRM SoE (primary) | `aion-infra` compose (CRM-001); Runtime Twenty adapter | Critical (PII/CRM) | **migrate** → **compliant** when private | Target primary SoE (ADR-019); deploy on `aion_internal`, DB ≠ `aion_data`; not SoR for intelligence |
| S-05 | OpenRouter (products + proof) | Frontier inference gateway | `aion-products` providers; `aion-runtime` proof script; infra → Copilot | Critical (prompts) | **migrate** | Default proprietary path today for Copilot; doctrine prohibits public API for proprietary tasks |
| S-06 | Anthropic Claude API | Frontier inference | `aion-products` `provider-adapter.ts` | Critical (prompts) | **migrate** | Direct SDK path when `ANTHROPIC_API_KEY` set |
| S-07 | Model readiness env probes | Config only | `aion-runtime` gateway | Low | **compliant** | Checks key presence; no production outbound LLM from Runtime host |
| S-08 | Local / owned LLM runtime | Missing capability | — | N/A | **migrate** | Phase 2 required; not present in any repo |
| S-09 | Agent memory / vector RAG store | Missing capability | `aion-data` deferred | N/A | **compliant*** | *Not built — build sovereign-first (Phase 2/4); do not introduce hosted vector SaaS |
| S-10 | Vercel (Operator Console, Revenue Copilot web, Desks) | Hosted delivery | `aion-products`, `aion-desks`, infra CORS | Medium–High (BFF bodies) | **migrate** | Temporary OK; self-hosted UI/BFF path required |
| S-11 | Hostinger VPS + Traefik + Let’s Encrypt | Compute / edge | `aion-infra` providers/vps | High (hosts SoR) | **compliant**† | †Single-vendor compute; portable profiles (AWS/GCP) dormant — vendor-independence incomplete |
| S-12 | AWS / GCP infra profiles | Portability (dormant) | `aion-infra` providers/aws, gcp | N/A live | **compliant** | Not provisioned; keep as recovery / portability options when GHL+auth wired |
| S-13 | Stripe Checkout + webhooks | Payments | `aion-desks` | High (buyer PII) | **optional-external** | Commerce only; no Runtime/GHL coupling |
| S-14 | PostHog analytics / flags | Analytics plane | ADR-010; Core `FeatureGate` port only | Medium if misconfigured | **exception-ADR-required** | Accepted ADR-010 vs ADR-016; prefer self-host/EU or exception ADR |
| S-15 | Backblaze B2 + GPG backups | Offsite recovery | `aion-infra` | High (ciphertext) | **optional-external** | Restore-tested; key custody proven 2026-09-25; second geography/vendor optional later |
| S-16 | ntfy.sh | Ops alerts | `aion-infra` | Low–Medium | **optional-external** | Replaceable; no proprietary payloads by design |
| S-17 | GitHub + GHCR | SCM / images | all repos | High (source/IP) | **exception-ADR-required** | Source of truth for code; access controls + AI-tool policy required |
| S-18 | Cursor / Claude Code / Grok / Codex harnesses | AI development tools | docs + Core harness port | Critical (architecture/source) | **migrate** | Doctrine: restrict protected repos/architecture/confidential context |
| S-19 | Google Fonts CDN | Browser CDN | products / desks HTML | Low | **optional-external** | Prefer self-host fonts in Phase 2 UI hardening |
| S-20 | Browser Web Speech API | STT vendor path | `aion-products` `useMic.ts` | High (audio) | **migrate** | Chrome→Google STT; replace with local STT or disable for proprietary calls |
| S-21 | Runtime HTTP gateway (0.0.0.0) | Execution surface | `aion-runtime` | High if public | **migrate** | Harden bind/network policy; offline/queue continuity unproven |
| S-22 | Operator Console custom auth | Shared-secret session | `aion-products` workforce-control BFF | Medium | **migrate** | Phase 2: AION-controlled identity plane (ADR-005 path) offline-capable |
| S-23 | npm registry / Node base images | Supply chain | all JS repos | Low | **optional-external** | Pin/lockfile discipline; not SoR |
| S-24 | Immich / n8n co-hosted on VPS | Adjacent apps | infra audits | Medium (shared host) | **migrate** | Reduce blast radius; isolate AION host |

\* Compliant only as “not yet introduced hosted dependency.” Building hosted Pinecone/etc. would flip to **migrate** / noncompliant.

---

## 2. Per-repo summary

### aion-core — **compliant**

- No outbound network, no DB SDK, no model SDK.
- Ports only: FeatureGate (PostHog-aware but empty), TelemetrySink, HarnessExecutionProvider, repos.
- GHL appears only as **catalog / side-effect contracts** (`provider: 'ghl'`), never as HTTP client.

### aion-data — **compliant**

- `pg` + hand-written SQL migrations; local Docker Postgres for dev; CI ephemeral Postgres.
- Explicit deferrals: Redis, Kafka, vectors, analytics warehouse.
- Docs mention Supabase-compatible Postgres as portability — **no Supabase client**.

### aion-runtime — **mixed**

| Area | Class |
|---|---|
| Postgres SoR via `@aion/data` | compliant |
| GHL live adapters | optional-external |
| OpenRouter in proof scripts | migrate (do not use for proprietary production inference) |
| OpenAI/Anthropic env probes | compliant (no outbound) |
| Public bind + auth mode | migrate (hardening + offline queues) |

### aion-infra — **mixed**

| Area | Class |
|---|---|
| VPS local Postgres Runtime path | compliant |
| Supabase legacy project + backups | migrate |
| GHL / OpenRouter secret injection split | optional-external / migrate (keys placement OK; destinations need policy) |
| B2 + GPG + ntfy | optional-external |
| AWS/GCP dormant | compliant (portability) |
| Full-host DR drill not complete | migrate (Phase 2/3 recovery) |

### aion-products — **noncompliant for proprietary inference**

| Area | Class |
|---|---|
| Anthropic + OpenRouter LLM paths | **migrate** (primary IP exposure) |
| Runtime client (first-party) | compliant |
| GHL (indirect via Runtime) | optional-external |
| Vercel hosting / BFF | migrate |
| Web Speech STT | migrate |
| Local JSON `AION_DATA_DIR` | compliant (local) |

### aion-desks — **optional-external commerce**

| Area | Class |
|---|---|
| Stripe | optional-external |
| Vercel | migrate (self-host path) |
| No Runtime / GHL / LLM | compliant (boundary held) |

### aion-docs / AI tooling — **policy gap**

| Area | Class |
|---|---|
| Doctrine + ADR-016 | compliant (governance) |
| GitHub-hosted constitution + architecture | exception-ADR-required |
| Cursor/Claude access to protected context | migrate |

---

## 3. Credential / secret *names* inventory (no values)

| Domain | Names (representative) | Custody today | Blast radius |
|---|---|---|---|
| Runtime DB | `DATABASE_URL`, `MIGRATION_DATABASE_URL`, `DATABASE_SSL` | VPS `/opt/aion/.env` | Full SoR |
| Gateway auth | `AION_GATEWAY_API_KEYS`, `AION_AUTH_MODE` | VPS env | All gateway ops |
| GHL | `GHL_API_KEY`, `GHL_LOCATION_ID`, `GHL_API_BASE_URL`, `GHL_BACKEND` | Runtime-only inject | CRM write/read |
| Frontier LLM | `OPENROUTER_API_KEY`, `OPENROUTER_MODEL`, `ANTHROPIC_API_KEY`, `AION_LLM_PROVIDER`, `AION_MODEL*` | Copilot / products env | Prompt exfiltration |
| Products ↔ Runtime | `AION_RUNTIME_URL`, `AION_RUNTIME_API_KEY`, `RUNTIME_URL`, `AION_GATEWAY_TOKEN` | Products / BFF | Command authority |
| Console auth | `CONSOLE_LOGIN_SECRET`, `SESSION_SIGNING_SECRET` | Vercel / BFF | Operator access |
| Desks commerce | Stripe secret + webhook secret + Price IDs | Vercel env | Payments |
| Deploy | `VERCEL_TOKEN`, GHCR login, VPS SSH | GitHub Actions | Deploy surface |
| Backup | B2 app key, GPG fingerprint/passphrase, rclone | Host + off-host (proven) | All backups |
| Alerts | ntfy topic | Host | Ops noise / signal |

---

## 4. IP exposure paths (ranked)

| Rank | Path | Data that can leave boundary | Doctrine status |
|---|---|---|---|
| P0 | Products → Anthropic / OpenRouter | Live call transcripts, objections, CRM-linked context, prompts | **Prohibited for proprietary** until local path exists |
| P0 | Runtime → LeadConnector (GHL) | Contacts, opportunities, notes, tasks, messages | Allowed only as **authorized SoE**; not intelligence SoR |
| P1 | Operator Console (Vercel BFF) ↔ Runtime | Login + proxied command bodies | Temporary delivery; harden + self-host |
| P1 | AI IDE tools (Cursor/Claude) ↔ repos/docs | Architecture, source, operational context | Restrict per doctrine |
| P1 | Legacy Supabase project | Historical company/ops data | Migrate / retire |
| P2 | Web Speech (Chrome STT) | Mic audio / transcripts | Migrate to local STT |
| P2 | Public git history | CRM identifiers historically | Hygiene (exposure review already noted) |
| P3 | Google Fonts / ntfy / Stripe metadata | Low / commerce | optional-external |
| P3 | B2 ciphertext | Encrypted dumps | Acceptable with key custody |

---

## 5. Stack directions (doctrine table) — audit result

| Component | Doctrine direction | Audit finding | Class |
|---|---|---|---|
| Supabase | Migrate critical data to AION-controlled | Runtime SoR already local Postgres; **legacy Supabase project still live + backed up** | **migrate** (legacy island) |
| Vercel | Temporary; self-hosted path | Active for products UIs + desks; BFF on Vercel | **migrate** |
| Grok / Claude / frontier | No public API for proprietary | Anthropic + OpenRouter **are** the proprietary Copilot path today | **migrate** |
| AION Runtime | Offline queues, policy, local recovery | Live gateway + GHL; offline/queues/local LLM **not proven** | **migrate** |
| Agent memory / RAG | Owned storage | Not built — opportunity to stay compliant | **compliant*** (build sovereign) |
| GHL / CRM | Migration SoE, not SoR | Dual-run until Twenty private SoE proven (ADR-019 / CRM-001) | **optional-external** |
| Twenty CRM | Primary SoE (self-hosted), not SoR | Not deployed yet; CRM-001 | **migrate** |
| AI development tools | Restrict protected context | No formal allowlist/deny for Cursor/Claude vs doctrine | **migrate** |

---

## 6. Phase 2 backlog (priority ordered)

| Priority | Backlog item | Owner (agent) | Exit signal |
|---|---|---|---|
| **P0** | **Local inference path** for proprietary Copilot/Revenue tasks (open-weight on controlled hardware or AION VPS); router default = local | AI Engineer + Infra | Proprietary prompts never hit OpenRouter/Anthropic |
| **P0** | **Restricted External Gateway policy** — classify GHL / Stripe / frontier as allowlisted capabilities with IP checks | Security + Runtime | Default-deny outbound; audit events on egress |
| **P0** | **Supabase legacy island plan** — inventory schemas still in `qbahthzqvxytfgobgtxa`; migrate or archive under AION keys; retire project | Data + Infra | No critical data solely on Supabase |
| **P1** | **Self-hosted Operator Console / Copilot UI** path (VPS or equivalent); keep Vercel as preview-only | Infra + Frontend | Production UI not dependent on Vercel |
| **P1** | **AI tooling access policy** — deny protected architecture/source to public AI unless scrubbed; document allowlists | Security + Architect | Written policy + enforcement checklist |
| **P1** | **Web Speech replacement or disable** for proprietary call capture | Product + AI | No browser-vendor STT for protected audio |
| **P1** | **ADR-010 vs ADR-016** — PostHog self-host/EU or exception ADR with data classification | Architect | Explicit decision recorded |
| **P1** | **GitHub / GHCR exception ADR** — time-bounded acceptance of SCM hosting + access controls | Architect + Security | Exception ADR or self-hosted git plan |
| **P2** | Offline execution queues + reconnect drain | Runtime | Phase 3 drill green |
| **P2** | AION-controlled identity for Operator Console (replace shared-secret-only) | Runtime + Security | Works offline from public IdP |
| **P2** | Owned RAG / memory schema in `aion-data` (no hosted vector SaaS) | Data + AI | Retrieval path under AION keys |
| **P2** | Isolate Immich/n8n from AION host or tighten blast-radius | Infra | Documented isolation |
| **P3** | Self-host fonts; reduce CDN egress | Frontend | Optional |
| **P3** | Second backup vendor / geography | Infra | Vendor independence hardening |
| **P3** | Activate AWS/GCP profiles only when mission-justified | Infra | Portability proof |

Suggested follow-on missions:

1. **SOV-002** — Local inference + model router (P0) — **contracted** ([ADR-017](../../adr/ADR-017-sovereign-model-router.md); [mission](../missions/SOV-002-local-inference-model-router.md))
2. **SOV-003** — Restricted External Gateway IP policy (P0) — **contracted** ([ADR-018](../../adr/ADR-018-restricted-external-gateway.md); [mission](../missions/SOV-003-restricted-external-gateway.md))
3. **SOV-004** — Supabase legacy retirement (P0)
4. **SOV-005** — Self-hosted UI delivery path (P1)
5. **SOV-006** — ADR-010/016 reconciliation (PostHog) (P1)

---

## 7. Phase 1 exit criteria checklist

- [x] Inventory covers core, data, runtime, infra, products, desks, AI tooling paths
- [x] Every master-inventory item labeled compliant | migrate | optional-external | exception-ADR-required
- [x] Stack directions addressed (Supabase, Vercel, frontier, Runtime, RAG, GHL, AI tools)
- [x] Priority-ordered Phase 2 backlog
- [x] No production shutdowns performed

---

## 8. Method & limits

**Method:** Static code search + package manifests + infra docs/recovery kits + prior exposure reviews across the seven workspace repos (2026-10-19).

**Limits:**

- Did not connect to live VPS, Supabase, Vercel, or GHL to enumerate runtime objects beyond docs.
- Did not decrypt backups or list live secret values (by design).
- Supabase schema contents need a live inventory ticket under SOV-004.
- “Compliant” for Hostinger compute assumes continued AION root access and tested backups; full clean-VPS rebuild drill remains open per `aion-infra` recovery kit.

**Sensitivity:** This inventory maps exposure paths. Treat as internal. Prefer leadership summary (Notion) without binding CRM project IDs in wide distribution when possible.
