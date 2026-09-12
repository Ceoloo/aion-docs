# Mission: Shared structured agent handoff

## OBJECTIVE

Land a single `AgentHandoff` contract so agent-to-agent (and Cursor specialist)
delegation carries `facts`, `uncertainties`, `artifactRefs`, and `confidence`
instead of chat replay.

## PRIMARY OWNER

`AION — AI` (AI / Agent Engineer)

## SUPPORTING

- Runtime — future transport via Command / ExecutionResult (handoff after Core)
- Data — durable storage only if audit retention is required later
- Docs — operating templates aligned to Core

## ACCEPTANCE

- [x] Zod `AgentHandoff` in `aion-core` with tests
- [x] `HandoffId` (`hof_…`) minted from identifiers
- [x] Confidence disposition helper (`escalate` / `accept` / `proceed_with_caution`)
- [x] `.aion` handoff standard + template + example JSON
- [x] Intelligence-layer + AI role card point at the contract
- [ ] Runtime ingress (follow-up mission)
- [ ] Data persistence (follow-up mission, only if needed)

## RISKS

- Parallel informal markdown shapes drifting from Core — mitigated by making
  Core the sole schema owner and docs a rendering of it.

## DELIVERABLE

Core PR + Docs PR for schemaVersion `1`.
