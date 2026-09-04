# Research → Design

External research does not enter AION as opinion. When a signal from the wider
agent-infrastructure field is strong enough to shape a boundary, a contract, or
the build order, it is captured here as a dated brief, and the architectural
response is recorded as ADRs, architecture documents, and per-repository design
specs — so anyone can trace **why** a decision exists back to the evidence that
motivated it.

This directory holds the briefs. It does **not** hold decisions: decisions live
in [`../adr/`](../adr/README.md), architecture in
[`../architecture/`](../architecture/README.md), and the concrete per-repo
design specs in each repository's `docs/design/` directory.

## Discipline

1. **Research informs; missions authorize.** A brief may raise a priority, but
   nothing speculative is *built* until a mission requires it
   ([Mission Before Infrastructure](../engineering/principles.md)). A brief that
   says "VERY HIGH — build now" still produces **design specs**, not production
   subsystems, until a mission attaches.
2. **Validation is not novelty.** Most external signals *validate* the existing
   AION doctrine rather than overturn it. A brief must state honestly what is
   genuinely new versus what merely confirms what we already decided.
3. **Every claim links to the response.** A brief ends with a table mapping each
   signal to the ADR / architecture doc / design spec it produced (or to an
   explicit "no change — already covered by …").

## Index

| Brief | Date | Drives |
|---|---|---|
| [2026 Runtime-Control Brief](2026-runtime-control-brief.md) | 2026-09-04 | ADR-003, ADR-004; execution-gateway, agent-economics, autonomy-tiers, agent-trace-schema; per-repo design specs |
