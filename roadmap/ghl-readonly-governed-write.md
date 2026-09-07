# GHL Phase A (read-only) → Phase B (governed write)

**Immediate milestone:** AION can read a tenant’s GHL state, propose one CRM
mutation, route it through the Execution Gateway, require approval when policy
demands it, execute it once, and record the result.

This is **not** a second runtime. It extends Mission 009’s GHL adapter +
`ExternalSideEffect` ledger.

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

**Proof:** `npm run proof:ghl-phase-ab` (A1–A4).

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

## Operator standing items

1. Rotate GHL + OpenRouter keys into `/opt/aion/.env` (`0600`)
2. For live reads/writes: set `GHL_API_KEY`, `GHL_LOCATION_ID`, `GHL_API_VERSION`
3. Keep `crm.message.send` behind stricter product policy even after keys exist

---

## Explicit non-goals (this slice)

- Continuous live-call streaming
- Appointment **writes** / calendar booking automation
- Autonomous customer messaging
- Counting this proof as OL-001 mission credit
