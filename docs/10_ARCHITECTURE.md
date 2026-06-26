# Architecture

> **Question this document answers:** What are the architectural styles and layers, and how do they combine?

This document defines the architectural foundation of the platform: the styles it
adopts and the layers it is built from. It is the structural baseline that the
rest of group 10 elaborates — `11_SYSTEM_OVERVIEW` names the runtime components,
`12_MODULES` decomposes them, and `17_DEPENDENCY_RULES` enforces the dependency
directions stated here.

---

## Architectural Styles

The platform combines four complementary styles. None is optional; each governs a
different axis of the design.

```
Modular Monolith  → one deployable, independently replaceable modules
Layered           → Domain · Application · Infrastructure · Interface
Clean             → dependencies point inward, toward abstractions
Event-Driven      → modules communicate primarily via domain events
```

- **ARCH-01** The platform MUST be built initially as a modular monolith — a single deployable composed of independently replaceable modules with enforced boundaries.
- **ARCH-02** The platform MUST follow a layered architecture with exactly four layers: `Domain`, `Application`, `Infrastructure`, and `Interface`.
- **ARCH-03** Dependencies MUST point inward (Clean Architecture): `Domain` depends on nothing; `Application` depends only on `Domain`; `Infrastructure` and `Interface` depend on `Application` and `Domain` abstractions, never the reverse.
- **ARCH-04** Modules MUST communicate primarily through domain events (`07_DOMAIN_EVENTS.md`); direct synchronous calls are reserved for defined intra-context interfaces.

## Layers

| Layer | Responsibility | May depend on |
|-------|----------------|---------------|
| Domain | Entities, value objects, invariants, pure business rules | nothing |
| Application | Use cases, orchestration, ports (interfaces) | Domain |
| Infrastructure | Persistence, transport, provider adapters, AI providers | Application, Domain (via ports) |
| Interface | Telegram, web, REST, WebSocket delivery | Application, Domain (via ports) |

- **ARCH-05** Business logic MUST depend only on abstractions; it MUST NOT reference any provider, venue, database, or transport concretion (`INV-09`).
- **ARCH-06** Each module MUST be independently replaceable behind its interface, including the AI provider, the venue, and the database (`01_PRODUCT_PHILOSOPHY.md` §6).

## How the Styles Combine

Layered defines *where* code lives; Clean defines *which direction* it may depend;
Event-Driven defines *how* modules talk across context boundaries; Modular
Monolith defines *how* it deploys today and migrates tomorrow.

- **ARCH-07** The architecture MUST remain coherent and functional with AI disabled (`INV-10`, `FR-AI-04`).
- **ARCH-08** Bounded contexts (`05_DOMAIN_MODEL.md`) MUST be preserved as seams so a future microservices split requires no business-logic rewrite, only redeployment of context boundaries (`ADR-014`).

---

## Related Documents

- `05_DOMAIN_MODEL.md` — the bounded contexts these layers respect
- `09_SYSTEM_INVARIANTS.md` — laws the dependency rules enforce
- `11_SYSTEM_OVERVIEW.md` — the runtime components realizing this architecture
- `17_DEPENDENCY_RULES.md` — the enforceable form of `ARCH-03`
- `ADR-001-Modular-Monolith.md` — the modular-monolith decision
- `ADR-013-Clean-Layered-Architecture.md` — the layering/dependency decision
