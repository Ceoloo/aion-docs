# MISSION-GROK-AUTONOMY-001 — Autonomous routines and governed one-click operations

**CEO decision:** 2026-10-16. **Status:** PROPOSED for production implementation; no native Grok routines or Runtime publishing connection has been activated by this PR.
**Priority:** P0 after GE-001 / Pilot-to-Revenue acceptance work. **Constraint:** existing bots only; no new seats or duplicate data plane.

## Outcome
AION's existing Grok workforce autonomously prepares and safely completes authorized repetitive work, automatically posts original low-risk content only within a revocable, exact-scope standing campaign grant, and sends the CEO one-click action cards only for meaningful decisions. Normal successes go to silent digest.

## Existing owners
- AION Media Intelligence: read-only daily X Opportunity Sweep at **08:00 ET**, source verified and deduplicated.
- Pipeline Ops / Revenue Architect: read-only Revenue Leak Report **weekdays at 09:00 ET**.
- Content Director / Media OS: evidence-backed content queue, conditional scheduling and authorized publishing.
- Brand + Evidence Guard: independent PASS / REVISE / BLOCK evaluation; QA / Security remain supporting reviewers.
- Chief of Staff: one exception-driven decision rollup; protect sprint capacity.
- Software Factory Lead: sandbox PR preparation; maintainer-gated merge/release.

## Rules of engagement
1. All production side effects through **AION Runtime**; Command Center is presentation, never a second authority. Existing Supabase revenue/activity/outcome/approval ledgers remain canonical. **Do not apply the prototype Grok SQL sketch.**
2. Auto publishing requires registered human-granted `tenant, actor, platform, account, campaign, content class, quota, expiry, grant version` and independent QA PASS; Runtime enforces grant, official platform adapter, idempotency, media rights and receipt verification. No blanket social publishing grant.
3. Otherwise one approval card for one exact action and immutable payload. `SEND`, `COLLECT` (collection request, not debit), `QUALIFIED`, `WON` each get a **distinct human authorization**, recipient/object and scope checks, and single-use idempotency token. These steps are alternatives, not four sequential mandatory steps.
4. Production changes, financial transfers, changes to security, legal commitments, client-sensitive communications and deletes remain human-gated. No model may approve itself.
5. Unattended native Grok Bot approval cards expire after ~10 minutes per current docs; design durable **AION Runtime approval inbox** and optional signed Slack buttons for asynchronous approval (proposed expiry 24h). Until integrated, do not claim persistent one-click approval.
6. API X Search costs require verified model, actual user-approved budget, external cap and logged usage before scheduled API use. Standalone probe is dry run by default.
7. No repeated unverified claims, automated unsolicited X replies, duplicate posts, unconsented outreach or catch-up posting bursts.

## Work packages
- [ ] Confirm each named existing Grok Bot, connected source and safe read-only skill. Test native X-DAILY-001 and REV-DAILY-001 routines; capture owner + next-run screenshot/evidence. **Not yet live.**
- [ ] Implement scoped standing publishing grant, independent PASS guard and pause-all switch in Core/Runtime; audit before/after, store platform post ID and URL.
- [ ] Implement four per-action approval policies and reusable immutable approval cards via Runtime + Workspace.
- [ ] Build policy-aware event routing: X signals → Content Director → reviewer → authorized publishing queue; revenue exceptions → four specific approval routes. Code/workflow ownership remains in canonical repos.
- [ ] Add cost ceilings, source freshness, durable retries/replay, dead-letter and notification deduping; critical-only push notifications.
- [ ] Wire measurement 24h/7d with source-linked published content, content-to-qualified-pipeline correlation, and actual CRM outcome reconciliation.
- [ ] Verify revocation, replay, expired card, cross-tenant, forged actor, changed payload, consent, 2x retry, API failure and duplicate post behavior.
- [ ] Release as a scoped internal pilot; do not expand to clients or more autonomous capabilities before evidence.

## Acceptance
- Two native Grok routines verified scheduled, with traceable next run, authentic current-data reports and no writes.
- One owned-account, officially authorized **auto-published** content item produces platform post ID/link; denial/expiry blocks unauthorized item.
- Four **separate** authenticated approval-card tests for SEND / COLLECT / QUALIFIED / WON, each idempotent and revocable. No broad CRM-write authorization.
- No duplicate canonical Supabase tables, no production migration applied, no deploy without approval.
- Evidence and outcomes visible in existing AION Workspace; routine completions do not cause noisy alerts.

**Reference guidance:** [Grok routines](https://docs.x.ai/grok-bot/skills-routines-and-automations) · [Grok approvals](https://docs.x.ai/grok-bot/approvals-security-and-privacy) · [X automation policies](https://help.x.com/en/rules-and-policies/x-automation)
