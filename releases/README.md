# Platform release manifests

Machine-readable evidence for immutable / release-candidate platform tags.

| Tag | Kind | Manifest |
|---|---|---|
| `execution-platform-v0.1.0` | immutable freeze | [execution-platform-v0.1.0.manifest.json](./execution-platform-v0.1.0.manifest.json) |
| `execution-platform-v0.2.0-rc1` | release candidate | [execution-platform-v0.2.0-rc1.manifest.json](./execution-platform-v0.2.0-rc1.manifest.json) |

## Rules

- **`execution-platform-v0.1.0` is immutable.** Do not move it. Fixes ship as later patches / later majors.
- **`execution-platform-v0.2.0-rc1` is a release candidate.** Do not move it. It certifies M001–M004 on the integration tip. It is **not** the final `execution-platform-v0.2.0` freeze.
- Promote to `execution-platform-v0.2.0` only after a clean post-tag validation and explicit final certification.
- RC1 next engineering checkpoint: **Mission 006 — Workforce Control Center** (after M005 economics).
