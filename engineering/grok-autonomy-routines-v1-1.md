# AION Grok OS — Native routine setup and approval contract v1.1

**Status:** Configurations prepared, NOT installed in the Grok account or connected to production. Supersedes the v1 read-only-only publishing rule **only for an authorized standing campaign grant**. Reuse current bots.

## X-DAILY-001 — existing AION Media Intelligence — daily 08:00 America/New_York
Paste into that Bot:
> Create a daily recurring routine for 8:00 AM America/New_York titled "AION X Opportunity Sweep". Search X read-only for the past seven days for specific buying problems among electrical contractors, home services, builders, creators, and AI-agent business adoption; include official developer announcements where relevant. Return five nonduplicative source-linked signals with post date, company/original confirmation, buyer pain, fit to AION service offers, counterevidence and confidence (OBSERVED versus INFERRED). Share verified opportunities with Content Director and Brand + Evidence Guard; never publish based only on trending keywords, send a DM, reply, like or follow. On unavailable source, report UNAVAILABLE, never stale-as-current. Deliver routine report to our established operations workspace silently, escalating only time-sensitive high-value decisions. Keep this a read-only routine, use only already approved usage budget, show the scheduled next run.

## REV-DAILY-001 — existing Pipeline Ops — weekdays 09:00 America/New_York
Paste into that Bot:
> Create a weekday 9:00 AM America/New_York recurring routine titled "AION Revenue Leak Report". Read only the authorized existing CRM and canonical ledger. Deduplicate leads, respect opt-outs and existing sequences, and report five highest-impact stale-lead/follow-up/proposal/stage leaks with exact record links, owner, next-best action, evidence freshness and useful message drafts. Do not send, enroll, collect, modify status or create new tables. Prepare separate Runtime-backed one-click proposals only for SEND, COLLECT, QUALIFIED, and WON, each bound to a single object and immutable payload. Fail visibly on stale/missing data. Send routine results to operations without notifying the CEO unless a materially blocked revenue action needs a decision. Show the scheduled next run.

## CONTENT-QUEUE-001 — existing Content Director — weekdays 11:00 ET
Paste into that Bot after Runtime publish adapter is verified:
> Create a weekday content queue routine at 11:00 AM America/New_York, but leave its publishing actions PAUSED until AION Runtime verifies an owned-account standing grant and an official supported publishing adapter. Draft from approved source-backed material, run independent Brand + Evidence Guard PASS / REVISE / BLOCK, check originality/licensing/consent and exact target. Auto-publish ONLY when Runtime confirms matching human-granted account/campaign/content class, quota, budget and nonexpired grant; use Runtime idempotent action and verify platform post ID/link. If no grant or QA PASS, hold and create one exact-content AION Runtime approval (if available); never bypass by directly using a logged-in Bot browser. No breaking news or trending-keyword automatic posts, no duplicative cross-posts or catch-up blasts. Measure 24h and 7d; notify CEO only on exceptions. Show status and next run.

## Technical authorization invariants
- **READ_ONLY**: research, reporting, verification, internal draft without mutations.
- **AUTO_PUBLISH**: signed standing grant per owner account/campaign with expiry, quota, post class, independent PASS, output hash, official adapter, single-use idempotency and published receipt; zero grants by default.
- **ONE_CLICK**: AION Runtime generates decision card for exact SEND / COLLECT / QUALIFIED / WON; each action distinct, authenticated, hashed, not reusable. Approval expired, canceled or changed payload fails closed. Do not grant implicit downstream permissions.
- **PROTECTED**: production deploy, secrets, legal/financial commitments and payment movements always require human gates.
- **ALERTS**: successful runs are silent; send one actionable card per truly blocked/urgent issue. MAX 2 revision attempts; no consent or sources → BLOCK.
- **Native approval limitations**: unattended Grok cards expire in about 10 minutes. AION Runtime/Workspace long-lived approval inbox (proposed 24h expiry) must be implemented and tested; Slack button callbacks must verify signatures, human identity and replay protections.
- **Provider use**: X official API only for X publishing (not browser scripting); xAI API research has usage cost; model must be verified; paid calls only with approved cap/cost accounting. Existing AION Supabase ledgers remain source of truth.

## Verify before calling it operational
1. Each routine appears in owning Grok Bot Routines tab; next-run is in ET; test run is read-only.
2. Source evidence + owner + execution receipt can be inspected.
3. Runtime rejects no grant, wrong account, changed payload, revoked grant, stage mismatch and replay.
4. First approved post gets real post ID/link; one-click card is authorized to actor and is single-use.
5. No unplanned push spam; no accidental sends, purchases, production changes or SQL migration.

Docs: https://docs.x.ai/grok-bot/skills-routines-and-automations | https://docs.x.ai/grok-bot/approvals-security-and-privacy
