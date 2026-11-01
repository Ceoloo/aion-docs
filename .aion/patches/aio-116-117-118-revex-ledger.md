# Patch: AIO-116 / AIO-117 / AIO-118 revex ledger integrity

**Status:** Hotfix packaged and locally proven in `aion-infra`. Live apply pending maintainer with Supabase migration credentials. Upstream mirror into `AION-Sys/Ceoloo-aion-revenue-copilot` pending write access.

## Bugs

1. **AIO-116** — CEO-attested email sends cannot enter the ledger; agents hit `gmail.send` prohibition when recording a human send.
2. **AIO-117** — `revex_mark_collected` emits `revenue.collected` for unknown ids and double-counts re-collects.
3. **AIO-118** — Logging a send never resolves `draft_to_send` waits.

## Artifact

See Ceoloo/aion-infra:

- `providers/supabase-legacy/migrations/20261101193000_aio116_117_118_revex_ledger_fixes.sql`
- `providers/supabase-legacy/test/run-ledger-fix-tests.sh` (local PASS)
- `providers/supabase-legacy/README.md` (capture + apply runbook)

## Apply order

1. Capture live `pg_get_functiondef` for `revex_log_outreach` / `revex_mark_collected` / `revex_ceo_waiting_on_human`.
2. Apply migration against `qbahthzqvxytfgobgtxa` (backup first).
3. Verify CEO waits shrink and touches / `revenue.collected` behave.
4. Mirror SQL into `AION-Sys/Ceoloo-aion-revenue-copilot/supabase/migrations/`.
5. Close AIO-116/117/118 with evidence.
