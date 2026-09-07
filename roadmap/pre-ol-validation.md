# PRE-OL validation status

Command Center is working. The first missions on the live surface are
**pre-OL validation**, not OL-001 production missions.

## Observed dashboard (honest read)

| Metric | Current | Interpretation |
|---|---|---|
| Missions | 3 | Runtime + Console path works |
| Executions | 7 → 8 after governance | Mission → executions persisted |
| Mission / execution success | was 100% | **Runtime** success — not revenue-production success |
| Human intervention | 0% → lit after governance | Governance can be exercised |
| Policy denials | 0 | Happy path only (until governance) |
| Pending approvals | 0 → 1 → 0 | Governance path verified |
| R2/R3 executions | R2 propose parked | Approval gate works |
| Avg latency | ~3 ms on PRE-OL set | Too low for real model / GHL / research I/O |
| Execution cost | 31 units | Local/stub cost accounting |
| Attributed EV / revenue influenced | 0 | No commercial outcome |

Visible mission IDs:

| ID / name | Role |
|---|---|
| `msn_d9aa3dac-5655-478f-a600-be018d9d2b2c` | Revenue Production v1 (Console) |
| `OL-001-M001` | Lead research (supervised) |
| `msn_c5409a36-1e16-48a8-af21-4d5b107cc863` | Revenue Production v1 (earlier) |

**Preserve** these records; classify / exclude from OL-001 progress (do not delete).

## Mission Control inspect (done)

All three missions inspected in Mission Detail:

| Check | Result |
|---|---|
| Metadata | `cohort=OL-001`, `productionEconomic` empty → treat as PRE-OL |
| Risk | All R1 on happy-path set |
| Approvals | 0 on those missions |
| Duration | ~0 ms start=complete |
| Outcome summary | **empty** on every inspected execution |
| Business artifacts | **none** (no company/contact/evidence/qualification payload) |

**Verdict:** hollow Runtime successes — infrastructure works; business output is not captured/displayed. Lead-research “success” ≠ useful research artifact.

Live Runtime host used by Console (ops note): `https://runtime.srv1655818.hstgr.cloud`
(not the docs hostname `runtime.aionsystems.ai` from this agent’s DNS view).

## What this proves

- Mission machinery is healthy: missions → executions → economics → dashboard
- Command Center reads **Runtime-derived** metrics (not invented UI state)
- Portfolio attribution / rollups are aggregating

## What this does **not** prove

- 100% success at revenue production
- Live model calls
- Live GHL I/O
- Approval / R2–R3 governance surfaces lighting up

Treat “100% success” as **system-test success**.

---

## Current status record

```text
AION PRE-OL VALIDATION
Missions:              3 (+ governance command)
Executions:            7 → 8
Runtime success:       hollow on PRE-OL set (empty outcomes)
Failures:              0 on PRE-OL set
Governance exercised:  YES — R2 crm.opportunity.update → REQUIRE_APPROVAL → approve
External GHL proof:    not yet (dry-run / seed ids only)
Live model proof:      not yet
Economic value:        0
OL-001 counted:        0 / 100
```

### Governance validation (done)

```text
PRE-OL command: crm.opportunity.update@1 (dryRun / seed opportunity)
  → decision REQUIRE_APPROVAL (R2)
  → pending approvals 0 → 1
  → explicit human approve (operator-console actor persisted)
  → pending 0; human interventions lit
```

Console Approve must send a full human `actor` matching `decidedBy` (actors FK).
Fixed in Operator Console ApprovalPanel.

Classification for these (and future Console launches while paused):

| Field | Value |
|---|---|
| `cohort` | `pre_ol_validation` |
| `synthetic` | `false` |
| `productionEconomic` | `false` |

OL-001 scoreboard counts **only** `cohort=OL-001` **and**
`productionEconomic=true`.

---

## Inspect before the next mission

In Mission Control, open each PRE-OL mission and verify:

```text
Mission
├── objective
├── service / workflow selected
├── execution count + status
├── agent identity
├── tenant
├── inputs
├── outputs / artifacts
├── tool calls
├── approvals
├── policy decisions
├── execution cost
└── terminal outcome
```

Especially lead-research style missions: a useful success artifact should
resemble company / contact / source evidence / industry / location / website /
qualification signals / pain hypotheses / recommended next action / confidence.
`status: succeeded` alone is **execution success without business output**.

---

## Next proofs (ordered)

### 1) Governance validation — DONE

R2 stage-propose parked on approval; Console metrics lit; explicit approve with
persisted human actor cleared the queue.

### 2) Standing GHL live gate — BLOCKED on PIT scopes

```text
REAL GHL TENANT
  → read contact/opportunity
  → normalize through adapter
  → propose one change
  → Execution Gateway
  → explicit approval
  → execute exactly once
  → audit (minimum fields in ghl-readonly-governed-write.md)
```

**Credential probe:** location `YK8RT5OnmQiMqprlyqYY` = **AION Empire** (PIT
auth OK). Contacts/opportunities/pipelines still return **scope 401** — expand
Private Integration scopes (see ghl-readonly-governed-write.md), install into
`/opt/aion/.env` on the Runtime host, redeploy with GHL env passthrough, then
re-run. **Rotate the chat-pasted PIT** after re-scoping.

Requires verified model access before any mission may count as OL-001.

Only after:

```text
GHL LIVE ✓
MODEL LIVE ✓
ACTIVATION GATE ✓
```

may the first mission become **OL-001 Mission 001 / 100**.

---

## Standing rules

- IE-002 stays closed
- OL-001 stays paused
- No new substrate unless Phase A→B proof exposes an actual blocker
- Stop generating only easy internal missions; next is governance, then real GHL
