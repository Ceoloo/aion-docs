# ADR-013: Command Center Configuration Authority and Staged Workforce Administration

- **Status:** Proposed
- **Date:** 2026-10-06
- **Decision Owners:** AION Systems / Security Infrastructure Layer

## Context

AION Command Center is becoming the human operating surface for the AION OS.
The platform already exposes substantial read and action APIs through
`aion-runtime`, including missions, approvals, executions, economics,
evaluations, routing, autonomy, side effects, implementation cases, outcomes,
revenue sessions, and—on newer Runtime code—the Agent Identity Registry.

Three constraints shape the next workforce-management milestone:

1. **Production release availability.** Production currently serves Runtime
   `20fe3a4...`. The Agent Identity Registry HTTP surface
   (`/v1/registry/agents...`) was added in later Runtime code
   (`e6ca874...`). A Command Center registry screen therefore cannot be
   production-functional until a separate Runtime release is deployed.

2. **Current registry authority is too broad for the Command Center.** Runtime's
   principal roles are currently `invoke | approve | register`. Registry
   upsert, suspend, revoke, and reactivate all require `register`. The
   dedicated Command Center principal was intentionally provisioned with only
   `invoke + approve`.

3. **Configuration governance must be server-enforced.** Permission, risk,
   policy, workflow, routing, product-schema, and other configuration changes
   cannot rely on UI-only conventions. Drafting, validation, approval, publish,
   versioning, and audit semantics must live in Runtime/Core/Data contracts.

The existing staged Command Center credential activation is intentionally
separate from any Runtime image change. GE-001 should be certified against the
currently pinned production release before a newer Runtime candidate is
introduced.

## Decision

1. **GE-001 certification precedes the Runtime release required by CC-002.**
   Command Center credential activation may reload the currently pinned image,
   but must not be combined with a newer Runtime release.

2. **CC-002 is split into two stages.**

   **CC-002a — Read-only Workforce Operations**
   - Agent Registry directory and detail views.
   - Registry completeness and governance review.
   - Effective security posture.
   - Trust Score views.
   - Routing recommendations and scorecards.
   - Autonomy grant visibility and eligibility evaluation where the existing
     API is non-mutating.
   - No agent permission expansion, risk-ceiling edit, registry upsert, or
     revocation-state mutation from the normal Command Center principal.

   CC-002a requires the separate Runtime release that includes the registry
   read APIs.

   **CC-002b — Governed Workforce Configuration**
   - Permission edits.
   - Tool/data-scope edits.
   - Action-tier edits.
   - Risk-ceiling changes.
   - Policy-version changes.
   - Agent registration/update.
   - Other authority-expanding configuration.

   CC-002b is blocked until AION has a server-side Configuration Change
   lifecycle: **draft → validate → authorize → human gate when required →
   publish → audit**. Command Center is the editor/diff surface; Runtime/Core
   remain the authority.

3. **Do not grant the Command Center principal the existing `register` role
   merely to unlock registry writes.** `register` currently combines
   authority-expanding registration/update with containment actions. That is
   too broad for the normal operator surface.

4. **Containment should become a separate least-privilege authority.**
   A follow-up Runtime/Core change should separate reducing authority from
   expanding/restoring authority. The intended model is:
   - **suspend / emergency contain:** narrow containment authority, allowed to
     reduce an agent's ability to act;
   - **reactivate / permission expansion / registration:** governed
     configuration authority, subject to the Configuration Change lifecycle;
   - **revoke:** treated as containment but may require stronger confirmation
     because of its durability/operational impact.

   The exact principal role name and endpoint contract (for example
   `contain`) is a follow-up implementation decision and must be tested
   fail-closed.

5. **The Configuration Plane is platform work, not frontend work.**
   - Core owns typed configuration/change contracts and authorization semantics.
   - Data owns durable versions, drafts, approvals/publish state, and audit
     lineage.
   - Runtime owns the HTTP management surface and applies published config.
   - Command Center owns forms, editors, previews, diffs, and operator UX.
   - Infra continues to own secret material and privileged deployment actions.

6. **Dashboard configuration never becomes direct code, DB, env, or shell
   mutation.** Source code defines schemas and executable behavior; the
   dashboard manages validated instances of those schemas.

## Alternatives Considered

| Option | Why rejected |
|---|---|
| Give Command Center `register` now | Bundles permission expansion and containment into one broad role; registry writes apply immediately with no configuration gate. |
| Enforce draft/gate/publish only in the frontend | A different client could bypass it; not a security or governance boundary. |
| Combine credential activation with the Runtime registry release | Changes two production variables at once and weakens GE-001 evidence on the pinned candidate. |
| Keep all configuration in source code permanently | Prevents AION from becoming an operable Company OS and forces engineers into routine business configuration. |
| Let Command Center write directly to Postgres/env | Violates Runtime/Core authority, auditability, and Secure Execution Layer boundaries. |

## Consequences

### Positive

- GE-001 evidence remains attributable to one pinned Runtime release.
- Command Center retains least privilege.
- Workforce administration can ship incrementally without creating an
  ungoverned permission editor.
- Configuration behavior becomes reusable across Command Center, APIs, agents,
  and future operator clients.
- Emergency containment can become faster without granting general registry
  administration.

### Negative

- CC-002b requires new Core/Data/Runtime contracts before the most powerful
  workforce controls are available.
- Registry mutation APIs need authorization refactoring.
- A new Runtime release is required even for CC-002a read-only registry views.
- Some configuration remains code-backed until migrated into typed durable
  configuration objects.

## Implementation Notes

Current state as of 2026-10-06:

- Production Runtime: `20fe3a435dab5f12175ae03998ad709398ce931d`.
- Registry routes exist in newer Runtime code including
  `e6ca874ca5a51afff06748038dab32318e691546`.
- Runtime principal roles: `invoke | approve | register`.
- Dedicated Command Center principal: `invoke + approve`.
- Registry upsert and revocation-state changes currently require `register`.

Recommended delivery order:

1. Activate Command Center credentials on the pinned production image.
2. Complete GE-001 and preserve its release/evidence chain.
3. Prepare and human-gate a separate Runtime release containing registry read
   surfaces.
4. Ship CC-002a.
5. Define Configuration Change contracts and persistence.
6. Add narrow containment authority.
7. Ship CC-002b only after the governed publish path passes acceptance.

## Follow-up Decisions

- Exact Configuration Change object schema and lifecycle states.
- Which configuration classes require mandatory human approval by risk level.
- Exact narrow containment role/capability name and whether revoke shares it.
- Rollback semantics for published configuration versions.
- Whether published configuration is hot-reloaded or activated by an explicit
  Runtime configuration refresh operation.
