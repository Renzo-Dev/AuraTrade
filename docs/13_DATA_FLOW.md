# Data Flow

> **Question this document answers:** How does data move from provider to learning?

This document traces the movement of data across the platform's components. It is
the *physical* companion to `14_DECISION_PIPELINE.md`: the pipeline defines the
business stages, this document defines how data physically travels between the
components of `11_SYSTEM_OVERVIEW.md` and where it is persisted.

---

## End-to-End Flow

```
Provider feed
  └▶ Market Adapter      standardize → Market Event        (persist: market storage)
       └▶ Feature Engine compute     → Feature             (persist: Feature Store)
            └▶ Analysis Engine        → Insight            (event: InsightGenerated)
                 └▶ Strategy Engine    → Recommendation
                      └▶ Decision Engine → Decision
                           └▶ Risk Engine  approve/reject
                                └▶ Execution Layer → Order/Outcome  (persist: event store)
                                     └▶ Learning Engine → Learning Record
```

- **FLOW-01** Data MUST flow forward along the pipeline; the only back-edge is the Learning Engine feeding historical knowledge into later analysis.
- **FLOW-02** Each transformation between components MUST emit the corresponding domain event (`07_DOMAIN_EVENTS.md`).
- **FLOW-03** Raw provider data MUST be standardized by an adapter before it enters any other component (`FR-CONN-04`).
- **FLOW-04** A Feature MUST be written to the Feature Store before any component consumes it (`INV-02`).
- **FLOW-05** Data MUST NOT skip a pipeline stage; every artifact passes through its predecessor (`FR-DEC-02`).
- **FLOW-06** Every artifact MUST carry traceability back to its originating Market Event (`FR-EXP-02`, `DM-02`).

## Persistence Touchpoints

| Stage | Hot path (Redis) | Durable store |
|-------|------------------|---------------|
| Market Event | latest tick cache | market storage (`43_MARKET_STORAGE.md`) |
| Feature | hot feature cache | Feature Store (`22_FEATURE_STORE.md`) |
| Insight → Decision | — | event store (`42_EVENT_STORAGE.md`) |
| Execution/Outcome | — | event store + relational (`40_DATABASE_SCHEMA.md`) |
| Learning Record | — | relational + time-series (`41_TIMESCALEDB.md`) |

- **FLOW-07** Hot-path reads MAY be served from Redis, but the durable store MUST remain the source of truth for replay (`NFR-QA-03`).

---

## Related Documents

- `11_SYSTEM_OVERVIEW.md` — the components data flows between
- `14_DECISION_PIPELINE.md` — the business stages this flow realizes
- `07_DOMAIN_EVENTS.md` — events emitted at each transformation
- `42_EVENT_STORAGE.md` — durable event persistence and replay
