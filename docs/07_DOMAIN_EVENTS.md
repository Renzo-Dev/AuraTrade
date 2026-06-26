# Domain Events

> **Question this document answers:** What domain events exist — publisher, subscribers, payload, delivery guarantees, and idempotency?

This document is the catalog of domain events the platform publishes. It is
distinct from `15_EVENT_BUS.md`, which defines the *transport contract*; this
document defines *which events exist* and their semantics. Events are the primary
way bounded contexts (`05_DOMAIN_MODEL.md`) communicate.

---

## Event Catalog

| Event | Publisher (context) | Primary subscribers | Payload (core fields) |
|-------|---------------------|---------------------|-----------------------|
| MarketEventReceived | Market | Feature Engine | instrument id, event type, timestamp |
| FeatureCalculated | Analysis | Analysis Engines, Feature Store | feature id, source event ids, value, timestamp |
| InsightGenerated | Analysis | Strategy Engine | insight id, engine, feature ids, payload |
| RecommendationCreated | Decision | Risk Engine | recommendation id, insight ids, action, confidence |
| DecisionApproved | Decision | Execution Layer | decision id, recommendation id |
| DecisionRejected | Decision | Learning Engine | decision id, recommendation id, reason |
| OrderSubmitted | Execution | venue adapter, audit | order id, decision id, instrument id |
| OrderFilled | Execution | Portfolio Engine | order id, fill price, quantity, timestamp |
| PositionOpened | Execution | Portfolio Engine | position id, instrument id, size |
| PositionClosed | Execution | Learning Engine | position id, realized pnl |
| OutcomeRecorded | Execution | Learning Engine | outcome id, execution id, result |

## Event Rules

- **DEVT-01** Every domain event MUST have exactly one publishing context; no event is published by two contexts.
- **DEVT-02** Every domain event MUST be immutable once published.
- **DEVT-03** Every domain event MUST carry a unique event id and a timestamp.
- **DEVT-04** Consumers MUST treat event handling idempotently: processing the same event id twice MUST have the same effect as processing it once.
- **DEVT-05** Every state transition defined in `06_STATE_MACHINE.md` MUST correspond to a published event in this catalog.
- **DEVT-06** Events MUST reference domain entities by id only; they MUST NOT embed provider- or venue-specific representations.

## Delivery Guarantees

- **DEVT-07** The platform MUST guarantee at-least-once delivery; combined with `DEVT-04`, this yields effectively-once processing.
- **DEVT-08** Event ordering MUST be preserved per entity (per aggregate id), not necessarily globally.

---

## Related Documents

- `05_DOMAIN_MODEL.md` — the entities these events reference
- `06_STATE_MACHINE.md` — the transitions these events correspond to
- `15_EVENT_BUS.md` — the transport contract that carries these events
- `42_EVENT_STORAGE.md` — how these events are persisted and replayed
