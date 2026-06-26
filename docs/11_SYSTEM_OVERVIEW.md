# System Overview

> **Question this document answers:** What are the runtime components and how do they fit together?

This document names the platform's runtime components and shows how they connect
at run time. It realizes the architecture from `10_ARCHITECTURE.md`: each
component is a module (`12_MODULES.md`) and communicates over the Event Bus
(`15_EVENT_BUS.md`). It describes *what runs*, not *how data is transformed* —
that is `13_DATA_FLOW.md`.

---

## Runtime Topology

```
Providers
   │  (raw data)
   ▼
Market Adapters ─▶ Feature Engine ─▶ Feature Store
                                        │
                                        ▼
                               Analysis Engines ◀─▶ AI Gateway ─▶ AI providers
                                        │  (insights)
                                        ▼
                                 Strategy Engine ─▶ Decision Engine
                                                        │ (recommendation)
                                                        ▼
                                                   Risk Engine  ── veto ──▶ Learning Engine
                                                        │ (approved decision)
                                                        ▼
                                                 Execution Layer ─▶ Provider adapters ─▶ Venues
                                                        │
                                            Portfolio Engine / Outcome ─▶ Learning Engine

        All cross-component messages traverse the Event Bus (Redis).
        Durable state: PostgreSQL + TimescaleDB. Hot path + transport: Redis.
```

## Component Inventory

| Component | Bounded context | Role |
|-----------|-----------------|------|
| Market Adapters | Market | translate provider data into standardized Market Events |
| Feature Engine | Analysis | compute Features from Market Events |
| Feature Store | Analysis | single source of truth for Features |
| Analysis Engines | Analysis | produce Insights, one domain each |
| AI Gateway | Analysis | abstract and replace external AI providers |
| Strategy Engine | Decision | turn Insights into candidate decisions |
| Decision Engine | Decision | select and sanction a Recommendation into a Decision |
| Risk Engine | Decision | absolute veto over every Decision |
| Execution Layer | Execution | route approved Decisions to venues (optional) |
| Portfolio Engine | Execution | manage Positions and allocation |
| Learning Engine | Infrastructure | turn Outcomes into reusable knowledge |
| Event Bus | Infrastructure | transport domain events between components |

- **SYS-01** The runtime MUST be composed of the components in the Component Inventory; each component MUST map to exactly one module (`12_MODULES.md`).
- **SYS-02** All inter-component communication MUST traverse the Event Bus or a defined interface; no component may reach into another's internals (`DM-07`).
- **SYS-03** The Risk Engine MUST sit between Decision and Execution on every path that can reach a venue (`INV-04`).
- **SYS-04** The Feature Store MUST be the only component that serves Features to other components (`INV-02`).
- **SYS-05** The AI Gateway MUST be the only component that communicates with external AI providers (`FR-AI-03`).
- **SYS-06** Market Adapters MUST be the only components holding provider- or venue-specific knowledge (`INV-09`, `FR-CONN-01`).
- **SYS-07** Durable state MUST use PostgreSQL with TimescaleDB; Redis MUST be used for event transport and the hot-path cache.
- **SYS-08** Optional components (Execution Layer, AI Gateway) MUST be disableable without halting the analysis path (`NFR-QA-01`).

---

## Related Documents

- `10_ARCHITECTURE.md` — the styles and layers these components realize
- `12_MODULES.md` — each component as a module with one responsibility
- `13_DATA_FLOW.md` — how data moves through these components
- `15_EVENT_BUS.md` — the transport that connects them
- `44_REDIS.md` — Redis as transport and cache
