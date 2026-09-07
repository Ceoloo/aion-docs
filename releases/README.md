# Platform release manifests

Machine-readable evidence for immutable / release-candidate platform tags.

| Tag | Kind | Manifest |
|---|---|---|
| `execution-platform-v0.1.0` | immutable freeze | [execution-platform-v0.1.0.manifest.json](./execution-platform-v0.1.0.manifest.json) |
| `execution-platform-v0.2.0-rc1` | release candidate | [execution-platform-v0.2.0-rc1.manifest.json](./execution-platform-v0.2.0-rc1.manifest.json) |
| `execution-platform-m006-complete` | integration baseline | [execution-platform-m006-complete.manifest.json](./execution-platform-m006-complete.manifest.json) |
| `execution-platform-v0.2.0` | immutable freeze | [execution-platform-v0.2.0.manifest.json](./execution-platform-v0.2.0.manifest.json) |

## Rules

- **`execution-platform-v0.1.0` is immutable.** Do not move it. Fixes ship as later patches / later majors.
- **`execution-platform-v0.2.0-rc1` is a release candidate.** Do not move it. It certifies M001–M004 on the integration tip. It is **not** the final `execution-platform-v0.2.0` freeze.
- **`execution-platform-m006-complete` is an immutable integration baseline** (M001–M006). Docs tip at tag time: `01bc090`. Do not move it.
- **`execution-platform-v0.2.0` is an immutable freeze** (M001–M009). Do not move it. Fixes ship as `v0.2.1+`.
- Next chapter after v0.2.0: **operating leverage** — real revenue/client workflows measured by throughput, intervention rate, failure rate, cost per mission, and economic value per execution.
