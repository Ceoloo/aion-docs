# System Context

This document draws the boundary of AION: who and what sits outside it, and how
they interact with it. It is the outermost view — one level above
[logical-architecture.md](logical-architecture.md).

```mermaid
flowchart TB
    subgraph Humans
        CEO["CEO / Founder<br/>(ultimate authority)"]
        OP["Human Operators<br/>(execution + approvals)"]
        CUST["Customers / Users"]
    end

    subgraph AION["AION Platform (sovereign boundary)"]
        CORE["Sovereign Control Plane"]
        INTEL["Intelligence Engine<br/>local LLMs · RAG · router"]
        DATA["Owned Data Layer"]
        PROD["Products / Workspace"]
        REG["Restricted External Gateway<br/>default deny"]
    end

    subgraph External["Optional external (non-proprietary only)"]
        LLM["Public model APIs<br/>(frontier — optional)"]
        AGENTS["External Agent Runtimes"]
        SAAS["Third-party APIs & SaaS"]
        CLOUD["Hosted cloud delivery<br/>(temporary where needed)"]
    end

    CEO -->|strategy, approvals| CORE
    OP -->|actions, approvals| CORE
    CUST -->|use| PROD

    CORE <-->|context, memory| INTEL
    CORE <-->|read/write| DATA
    PROD -->|consume primitives| CORE
    PROD -->|read canonical data| DATA

    INTEL -->|local / owned inference| INTEL
    CORE --> REG
    REG -.->|policy-allowed only| LLM
    REG -.->|policy-allowed only| AGENTS
    REG -.->|policy-allowed only| SAAS
    AION -.->|self-hosted path required| CLOUD
```

## External actors

| Actor | Relationship to AION |
|---|---|
| **CEO / Founder** | Holds ultimate authority. Sets strategy; approves financial, destructive, and high-risk actions via [human gates](../governance/human-gates.md). |
| **Human operators** | Execute work AION routes to them and grant approvals at gates. They are a first-class execution environment, not an afterthought. |
| **Customers / users** | Interact only with **products**, never with the control plane directly. |

## External systems

| System | Role | Coupling rule |
|---|---|---|
| **Local / owned model runtimes** | Primary inference for proprietary work. | Required path for core intelligence ([doctrine](sovereign-intelligence-doctrine.md)). |
| **Public model providers** | Optional inference for **non-proprietary** tasks only. | Never mandatory; never receive protected IP. |
| **External agent runtimes** | Claude Code, Cursor, Codex, Grok, Kimi/OpenClaw, etc. Perform execution work. | Interchangeable behind a common interface; protected IP restricted. |
| **Third-party APIs / SaaS** | CRMs, payment, comms, analytics vendors. | Explicitly authorized externals; never system of record for protected intelligence. |
| **Cloud / hosted delivery** | May host temporarily; self-hosted path required. | Infrastructure supports the architecture; it does not dictate it. Owned by `aion-infra`. |

## Boundary rules

1. **Customers touch only products.** The control plane, intelligence, and data
   layers are internal.
2. **External runtimes are pluggable.** No AION logic assumes a specific agent
   runtime or model. See [execution-layer.md](execution-layer.md).
3. **Every external call is a governed tool call** through the Restricted
   External Gateway (default deny). It carries identity, permissions,
   observability, and an IP-exposure check. See
   [security-model.md](security-model.md) and
   [sovereign-intelligence-doctrine.md](sovereign-intelligence-doctrine.md).
4. **No external system owns canonical business entities.** AION mirrors what it
   needs into `aion-data` with explicit ownership. See
   [data-layer.md](data-layer.md).
5. **Core operations remain viable offline.** External communications may queue
   until connectivity returns.
