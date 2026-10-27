# Audit patches

Apply these against the named repository when the Cloud Agent cannot push
directly (missing write permission on that remote).

| Patch | Target repo | Linear |
|---|---|---|
| [aio-106-action-service-auth-boundary.patch](aio-106-action-service-auth-boundary.patch) | `Ceoloo/aion-action-engine` | [AIO-106](https://linear.app/aion-empire/issue/AIO-106) |

```bash
cd aion-action-engine
git checkout -b cursor/action-service-auth-boundary-6eba
git am ../aion-docs/.aion/audits/patches/aio-106-action-service-auth-boundary.patch
pnpm install && pnpm -r --filter=./packages/* build && pnpm --filter @aion/action-service test
git push -u origin HEAD
```
