# Domain Model

> **Question this document answers:** What are the core domain entities, their relationships, and bounded contexts?

This document defines the platform's domain entities and how they relate. It is the structural companion to `04_GLOSSARY.md`: the glossary defines terms, this document defines the entity graph and the bounded contexts that group those entities. It is provider- and storage-independent — pure domain, no persistence concerns.

---

## Entity Graph

The core entities form a single directed chain that mirrors the decision pipeline:

```
Market → Instrument → Market Event → Feature → Insight → Recommendation
       → Decision → Execution → Position → Outcome → Learning Record
```

- **DM-01** Each entity MUST be a provider-agnostic domain model; no entity may carry provider- or venue-specific fields.
- **DM-02** Each `Feature` MUST reference the `Market Event`(s) it was derived from, so that any downstream artifact is traceable to its originating event.
- **DM-03** Each `Recommendation` MUST reference the `Insight`(s) that justify it.
- **DM-04** Each `Decision` MUST reference exactly one `Recommendation`.
- **DM-05** Each `Execution` MUST reference the `Decision` that authorized it.
- **DM-06** Each `Outcome` MUST reference the `Execution` it realizes.

## Entity Reference

| Entity | Identity | Key relationships |
|--------|----------|-------------------|
| Market | market class (e.g. crypto, forex) | groups Instruments |
| Instrument | symbol within a Market | source of Market Events |
| Market Event | event id | belongs to Instrument; input to Features |
| Feature | feature id + timestamp | derived from Market Events; consumed by Insights |
| Insight | insight id | produced by an Analysis Engine from Features |
| Recommendation | recommendation id | combines Insights; carries action/confidence/reasons/warnings/evidence/horizon |
| Decision | decision id | selects exactly one Recommendation |
| Execution | execution id | realizes a Decision at a venue |
| Position | position id | opened/closed by Executions |
| Outcome | outcome id | realized result of an Execution |
| Learning Record | record id | persists the full chain for future analysis |

## Bounded Contexts

Entities are grouped into bounded contexts. Each context is an independent area
of responsibility and a candidate seam for a future microservices split.

```
Market context        : Market, Instrument, Market Event
Analysis context      : Feature, Insight
Decision context      : Recommendation, Decision
Execution context     : Execution, Position, Outcome
Infrastructure context: Learning Record, cross-cutting persistence/transport
```

- **DM-07** A context MUST own its entities; another context MUST interact with them only through published domain events or defined interfaces, never by reaching into their internals.
- **DM-08** The `Decision` context MUST NOT depend on any specific provider or venue; only the `Execution` context, via adapters, touches venues.

---

## Related Documents

- `04_GLOSSARY.md` — definitions of every term used here
- `06_STATE_MACHINE.md` — lifecycles of Recommendation, Order, and Position
- `07_DOMAIN_EVENTS.md` — events published between these contexts
- `14_DECISION_PIPELINE.md` — the pipeline this entity chain mirrors
- `17_DEPENDENCY_RULES.md` — how context boundaries are enforced in code
