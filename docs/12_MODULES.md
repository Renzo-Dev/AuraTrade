# Modules

> **Question this document answers:** What modules exist and what is each responsible for?

This document decomposes the runtime components of `11_SYSTEM_OVERVIEW.md` into
modules and fixes each module's single responsibility and interface. A module is
the unit of independent replaceability; this document is the contract for *what a
module owns*, while `17_DEPENDENCY_RULES.md` governs *what it may depend on*.

---

## Module Catalog

| Module | Bounded context | Single responsibility | Primary interface (consumes → produces) |
|--------|-----------------|-----------------------|------------------------------------------|
| market-adapter | Market | standardize provider data | provider feed → `MarketEventReceived` |
| feature-engine | Analysis | compute Features | `MarketEventReceived` → `FeatureCalculated` |
| feature-store | Analysis | persist/serve Features | `FeatureCalculated` → Feature reads |
| analysis-engine | Analysis | produce Insights (one domain) | Features → `InsightGenerated` |
| ai-gateway | Analysis | abstract AI providers | prompt → provider response |
| strategy-engine | Decision | propose candidate decisions | `InsightGenerated` → Recommendation |
| decision-engine | Decision | sanction a Recommendation | Recommendation → `RecommendationCreated` |
| risk-engine | Decision | veto or approve Decisions | Recommendation → `DecisionApproved`/`DecisionRejected` |
| execution-layer | Execution | route approved Decisions | `DecisionApproved` → `OrderSubmitted` |
| portfolio-engine | Execution | manage Positions | `OrderFilled` → `PositionOpened`/`PositionClosed` |
| learning-engine | Infrastructure | persist the full chain | terminal events → Learning Records |
| event-bus | Infrastructure | transport events | publish → deliver |

- **MOD-01** Each module MUST have exactly one responsibility and belong to exactly one bounded context (`05_DOMAIN_MODEL.md`).
- **MOD-02** A module MUST expose its capability only through a defined interface; callers MUST NOT reach into its internals (`DM-07`).
- **MOD-03** A module MUST declare its dependencies explicitly; hidden or global coupling is prohibited.
- **MOD-04** Removing or replacing a module MUST NOT require edits to unrelated modules (`01_PRODUCT_PHILOSOPHY.md` §6).
- **MOD-05** Two modules MUST NOT share mutable state directly; shared data MUST flow through events or a store interface.
- **MOD-06** Each module MUST be independently testable in isolation (`NFR-TEST-01`).
- **MOD-07** A module that is optional (e.g. `ai-gateway`, `execution-layer`) MUST declare itself optional so the platform can run without it (`NFR-QA-01`).
- **MOD-08** Module boundaries MUST align with bounded-context boundaries so they remain valid microservice seams (`ADR-014`).

---

## Related Documents

- `11_SYSTEM_OVERVIEW.md` — the components these modules implement
- `05_DOMAIN_MODEL.md` — the bounded contexts each module belongs to
- `07_DOMAIN_EVENTS.md` — the events that cross module boundaries
- `17_DEPENDENCY_RULES.md` — what each module may depend on
