# Untrusted Model Security — core AION pillar

Status: proposed baseline amendment; binding when merged through normal governance.
Owner: TechOps / Security, with Core policy and Runtime enforcement owners.
Applies to every new or changed model, agent, tool, runner, connector, and deployment.

**Assume a model can be compromised. Limit the damage through controls outside the model.**
Local hosting preserves control over data placement; it does not establish trust
in weights, templates, parsers, dependencies, or tool decisions. A model proposes;
AION authenticates, authorizes, executes, and records. This extends the existing
[security model](security-model.md), not a second policy authority.

## Required controls and evidence

| ID | Requirement | Owner | Release evidence |
|---|---|---|---|
| UMS-01 | Admit only reviewed model sources for tool-enabled work. Record publisher, exact revision, artifact digest, format, license, quantizer/fine-tune lineage, reviewer and review date. Unverified artifacts stay in isolated research without production tools/data. | Intelligence / TechOps | Versioned admission record; mismatched artifact is rejected. A checksum proves integrity, not publisher intent. |
| UMS-02 | Review the actual tokenizer/chat template and loader shipped in the artifact. Record a separate template digest; re-review changes. Disable remote repository code by default. Never execute README installers automatically. | Intelligence | Extracted template review, pinned loader, sandboxed parser test and deny test for unapproved executable loading. |
| UMS-03 | Pin runner/container/dependencies to reviewed immutable versions. Track advisories and patch before promotion; verify signatures/provenance where available. Keep inference separate from business credentials and execution. | Infra | Inventory, source/digest verification, advisory review date, rollback artifact and isolation test. |
| UMS-04 | Unauthenticated development endpoints listen only on loopback, validate Host, and reject unapproved browser origins before dispatch. Remote/container access requires authentication and private routing or an authenticated TLS edge. No unauthenticated host port publication. | Runtime / Infra | Listener inspection plus negative Host, Origin, no-token and off-host tests. CORS is not authentication. |
| UMS-05 | Model output cannot grant authority. Gateway derives actor/tenant from authenticated identity and durable grants, validates exact tool arguments/resources, and denies unknown capabilities. High-impact approval binds to the exact action and expires; material changes require new approval. | Core / Runtime | Forged identity, cross-tenant, revoked actor, changed-action and replay denial tests. |
| UMS-06 | Workers have no business API keys, SSH keys, cloud credentials, migration credentials, Docker socket or host-home mounts. Broker credentials inside narrowly scoped adapters. Mount only task data; use non-root, bounded compute, restricted filesystem and short-lived credentials where supported. | Infra / Runtime | Effective environment/mount/identity inspection using synthetic secret canaries, not real-secret dumping. |
| UMS-07 | Separate arbitrary code execution from network-enabled credential brokers. An untrusted code sandbox must not combine unrestricted network access with sensitive reads. Egress policy checks destination, method, resource, payload classification and redirects; trusted domains alone are insufficient. | Infra / Runtime | Denied exfiltration test including an otherwise approved domain, redirect tests, network policy and broker audit evidence. |
| UMS-08 | Log actor, tenant, tool, policy/model/template versions, approval reference and outcome without secrets or raw sensitive payloads. Provide revocation and containment independent of the model. | Runtime / Assurance | Revoke-then-invoke denial, containment drill, secret-redaction check and trace reconstruction. |

## Builder and review gate

Every affected PR records: changed trust boundary; worst reachable damage;
artifact provenance; effective tools, credentials, mounts and egress; deterministic
denial tests; remaining gaps; and rollback/containment procedure. Mark inapplicable
controls with evidence. Missing evidence is **unknown**, never a pass. Model
benchmarks and prompt instructions do not replace enforcement.

Test a malicious tool proposal directly: arbitrary command execution, secret-file
read, cross-tenant lookup, unapproved outbound request, approval replay and a
revoked identity. These tests must fail before side effects, even if the model
insists the action is safe. Good evals cannot prove absence of a hidden backdoor.

New tool-enabled models/runners remain in research mode until applicable controls
are evidenced. Existing gaps require an owner, mitigation and due date under the
existing exception/change process; agents cannot waive their own controls.

## Response to suspected compromise

Suspend the worker/identity and block its execution/egress; preserve restricted
logs and artifact digests; identify reachable credentials/data; rotate exposed
credentials through their owners; quarantine artifacts; restore a reviewed build;
rerun boundary and revocation tests before restoring authority. Do not run a
suspect model or installer on a credential-bearing workstation to investigate it.

## Sources and limits

- [Requested video](https://youtu.be/3mx84MRj8lY), transcript reviewed 2026-10-28:
  layered risks in files, templates, runners, network exposure and tools.
- [OWASP AI Agent Security](https://cheatsheetseries.owasp.org/cheatsheets/AI_Agent_Security_Cheat_Sheet.html):
  independent tool authorization, scoped permissions, high-impact integrity and adversarial validation.
- [Docker port publishing](https://docs.docker.com/engine/network/port-publishing/):
  default port exposure and loopback publication caveats.

The video's individual incident counts, dates, exploit claims and version numbers
are not adopted as verified facts or patch thresholds. Consult the runner vendor's
current advisories for the exact deployed version. This document specifies a
baseline; it is not evidence that all deployments already enforce it.
