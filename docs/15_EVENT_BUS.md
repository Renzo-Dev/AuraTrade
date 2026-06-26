# Event Bus

> **Question this document answers:** What is the event *transport* contract between modules?

This document defines the transport contract for domain events — the envelope,
the delivery guarantees, and the subscription model. It is deliberately distinct
from `07_DOMAIN_EVENTS.md`, which catalogs *which events exist*; this document
governs *how* any such event travels between modules.

---

## Event Envelope

Every published message carries a transport-neutral envelope:

```
event_id      : unique id (idempotency key)
event_type    : name from the 07 catalog (e.g. OrderFilled)
aggregate_id  : entity id used for per-aggregate ordering
occurred_at   : timestamp
payload       : event-specific fields (see 07 catalog)
```

- **EVT-01** The Event Bus MUST transport the events defined in `07_DOMAIN_EVENTS.md`; it MUST NOT define new event semantics of its own.
- **EVT-02** The Event Bus MUST provide at-least-once delivery (`DEVT-07`).
- **EVT-03** The Event Bus MUST preserve ordering per `aggregate_id`, not necessarily globally (`DEVT-08`).
- **EVT-04** Publishers MUST NOT know their subscribers; delivery MUST be by `event_type` subscription, keeping publisher and consumer decoupled.
- **EVT-05** The transport MUST be Redis in the modular monolith, but the envelope and subscription contract MUST NOT depend on Redis specifics, so the transport is replaceable (`ADR-001`, `01_PRODUCT_PHILOSOPHY.md` §6).
- **EVT-06** Payloads MUST be serialized in a transport-neutral format; consumers MUST tolerate unknown additive fields to allow schema evolution.
- **EVT-07** A failing consumer MUST NOT block delivery to other consumers of the same event (`NFR-QA-02`).
- **EVT-08** Every published event MUST be immutable in transit; the bus MUST NOT mutate payloads (`INV-08`, `DEVT-02`).

## Subscription Model

| Property | Rule |
|----------|------|
| Routing | by `event_type` |
| Fan-out | one event → many independent consumers |
| Retry | per-consumer, bounded; idempotency via `event_id` (`DEVT-04`) |
| Ordering scope | per `aggregate_id` |

---

## Related Documents

- `07_DOMAIN_EVENTS.md` — the catalog of events this bus carries
- `09_SYSTEM_INVARIANTS.md` — `INV-08`, the single-publisher/immutability law
- `42_EVENT_STORAGE.md` — durable persistence of transported events
- `44_REDIS.md` — Redis as the concrete transport
