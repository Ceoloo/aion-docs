# Platform release manifests

Machine-readable evidence for immutable / release-candidate platform tags.

| Tag | Kind | Manifest |
|---|---|---|
| `execution-platform-v0.1.0` | immutable freeze | [execution-platform-v0.1.0.manifest.json](./execution-platform-v0.1.0.manifest.json) |
| `execution-platform-v0.2.0-rc1` | release candidate | [execution-platform-v0.2.0-rc1.manifest.json](./execution-platform-v0.2.0-rc1.manifest.json) |
| `execution-platform-m006-complete` | integration baseline | [execution-platform-m006-complete.manifest.json](./execution-platform-m006-complete.manifest.json) |
| — | cross-repo pin lock | [platform-contract-lock.json](./platform-contract-lock.json) — the one core/data commit every consumer pins |
| `governed-execution-candidate` | **UNVERIFIED** candidate pin | [governed-execution-candidate.manifest.json](./governed-execution-candidate.manifest.json) — fill production identity after deploy; not a PASS |

## Rules

- **`execution-platform-v0.1.0` is immutable.** Do not move it. Fixes ship as later patches / later majors.
- **`execution-platform-v0.2.0-rc1` is a release candidate.** Do not move it. It certifies M001–M004 on the integration tip. It is **not** the final `execution-platform-v0.2.0` freeze.
- **`execution-platform-m006-complete` is an immutable integration baseline** (M001–M006). Docs tip at tag time: `01bc090`. Do not move it.
- Promote to `execution-platform-v0.2.0` only after a clean post-tag validation and explicit final certification.
- **`governed-execution-candidate` is not certified.** Update `result` and `productionIdentity` only after a live deploy verify; promote to an immutable tag only after GE-001 PASS.
- Next checkpoint after M009: prefer promoting **`execution-platform-v0.2.0`** after clean validation (governed → … → external execution). M006-complete baseline remains immutable; do not move tags casually.
