# Phase B — Group 10 Architecture Documentation Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Author the eight Group 10 Architecture documents (`10_ARCHITECTURE` … `17_DEPENDENCY_RULES`) under `docs/`, each conforming to the Documentation Style Contract, so the architecture baseline is complete and internally consistent with the Foundation docs (`05`–`09`) and Stage 0 (`00`–`04`).

**Architecture:** Documentation-only. Each task authors exactly one document with verbatim content provided in the task body, verifies it against the Style Contract with `grep`, and commits. A final consistency pass verifies the group cross-references resolve and normative IDs are unique. No source code is produced.

**Tech Stack:** Markdown. Verification via `grep`/`bash`. Git for version control.

## Global Constraints

- **One question per document.** Each doc answers exactly one question, stated in its `> **Question this document answers:**` line. Never mix concerns.
- **Style Contract template:** `# <Title>` → `> **Question…**` → 1–2 intro paragraphs → `---` → topic sections → `---` → `## Related Documents`.
- **Normative language** (MUST / MUST NOT / SHOULD / MAY) reserved for normative clauses, not descriptive prose.
- **Stable IDs**, one prefix per document, no duplicate definitions: `10`=`ARCH-`, `11`=`SYS-`, `12`=`MOD-`, `13`=`FLOW-`, `14`=`PIPE-`, `15`=`EVT-`, `16`=`CFG-`, `17`=`DEP-`.
- **Vocabulary comes from `04_GLOSSARY.md`.** Do not introduce undefined terms.
- **Contracts are language-neutral:** field/endpoint/column tables or YAML-like blocks. No Go/Python code in document bodies.
- **Diagrams are ASCII only.** No external renderers.
- **Every document ends** with a `## Related Documents` block whose links resolve to files that exist or are planned in the spec inventory (`00`–`69`, ADRs).
- **No contradiction** of a locked decision (spec §1), a system invariant (`09`), an NFR (`08`), or the glossary (`04`).
- Work from `/home/renzo/gitProjects/AuraTrade`. Each task commits its own file(s).

---

### Task 1: Author `10_ARCHITECTURE.md`

**Files:**
- Create: `docs/10_ARCHITECTURE.md`

**Interfaces:**
- Consumes: bounded contexts and `DM-07`/`DM-08` from `05_DOMAIN_MODEL.md`; `INV-09`/`INV-10` from `09`; locked stack decisions (spec §1); ADR-001/010/013/014.
- Produces: the layer names (`Domain`, `Application`, `Infrastructure`, `Interface`) and the four architectural styles referenced by `11`, `12`, and `17`. Clause prefix `ARCH-`.

- [ ] **Step 1: Write the document**

Create `docs/10_ARCHITECTURE.md` with exactly this structure and content:

````markdown
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
````

- [ ] **Step 2: Verify structure and clause count**

Run: `f=docs/10_ARCHITECTURE.md; grep -q "^# Architecture" $f && grep -q "Question this document answers" $f && grep -q "## Related Documents" $f && grep -cE "\*\*ARCH-[0-9]" $f`
Expected: no error, final number is `8`.

- [ ] **Step 3: Verify no duplicate ID definitions**

Run: `grep -oE "\*\*ARCH-[0-9]+\*\*" docs/10_ARCHITECTURE.md | sort | uniq -d`
Expected: empty output.

- [ ] **Step 4: Commit**

```bash
git add docs/10_ARCHITECTURE.md && git commit -m "docs: add 10_ARCHITECTURE with styles, layers, and their combination"
```

---

### Task 2: Author `11_SYSTEM_OVERVIEW.md`

**Files:**
- Create: `docs/11_SYSTEM_OVERVIEW.md`

**Interfaces:**
- Consumes: layers/styles from `10`; invariants `INV-02`/`INV-04`/`INV-09` from `09`; component vocabulary from `04_GLOSSARY.md`; stack from spec §1.
- Produces: the canonical runtime component names referenced by `12_MODULES` and `13_DATA_FLOW`. Clause prefix `SYS-`.

- [ ] **Step 1: Write the document**

Create `docs/11_SYSTEM_OVERVIEW.md` with exactly this structure and content:

````markdown
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
````

- [ ] **Step 2: Verify structure and clause count**

Run: `f=docs/11_SYSTEM_OVERVIEW.md; grep -q "Question this document answers" $f && grep -q "## Related Documents" $f && grep -cE "\*\*SYS-[0-9]" $f`
Expected: no error, final number is `8`.

- [ ] **Step 3: Verify no duplicate ID definitions**

Run: `grep -oE "\*\*SYS-[0-9]+\*\*" docs/11_SYSTEM_OVERVIEW.md | sort | uniq -d`
Expected: empty output.

- [ ] **Step 4: Commit**

```bash
git add docs/11_SYSTEM_OVERVIEW.md && git commit -m "docs: add 11_SYSTEM_OVERVIEW with runtime topology and component inventory"
```

---

### Task 3: Author `12_MODULES.md`

**Files:**
- Create: `docs/12_MODULES.md`

**Interfaces:**
- Consumes: component names from `11`; bounded contexts and `DM-07` from `05`; philosophy §6.
- Produces: the module/responsibility mapping referenced by `17_DEPENDENCY_RULES`. Clause prefix `MOD-` (distinct from `NFR-MOD-` in `08`).

- [ ] **Step 1: Write the document**

Create `docs/12_MODULES.md` with exactly this structure and content:

````markdown
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
````

- [ ] **Step 2: Verify structure and clause count**

Run: `f=docs/12_MODULES.md; grep -q "Question this document answers" $f && grep -q "## Related Documents" $f && grep -cE "\*\*MOD-[0-9]" $f`
Expected: no error, final number is `8`.

- [ ] **Step 3: Verify no duplicate ID definitions and no `NFR-MOD` collision**

Run: `grep -oE "\*\*MOD-[0-9]+\*\*" docs/12_MODULES.md | sort | uniq -d; grep -c "NFR-MOD" docs/12_MODULES.md`
Expected: empty first line (no duplicates), then `0` (no `NFR-MOD` clause leaked in).

- [ ] **Step 4: Commit**

```bash
git add docs/12_MODULES.md && git commit -m "docs: add 12_MODULES with module catalog and responsibilities"
```

---

### Task 4: Author `13_DATA_FLOW.md`

**Files:**
- Create: `docs/13_DATA_FLOW.md`

**Interfaces:**
- Consumes: components from `11`; events from `07`; `INV-02`; `FR-CONN-04`, `FR-EXP-02`, `DM-02`.
- Produces: the data-movement narrative referenced by `14_DECISION_PIPELINE`. Clause prefix `FLOW-`.

- [ ] **Step 1: Write the document**

Create `docs/13_DATA_FLOW.md` with exactly this structure and content:

````markdown
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
````

- [ ] **Step 2: Verify structure and clause count**

Run: `f=docs/13_DATA_FLOW.md; grep -q "Question this document answers" $f && grep -q "## Related Documents" $f && grep -cE "\*\*FLOW-[0-9]" $f`
Expected: no error, final number is `7`.

- [ ] **Step 3: Verify no duplicate ID definitions**

Run: `grep -oE "\*\*FLOW-[0-9]+\*\*" docs/13_DATA_FLOW.md | sort | uniq -d`
Expected: empty output.

- [ ] **Step 4: Commit**

```bash
git add docs/13_DATA_FLOW.md && git commit -m "docs: add 13_DATA_FLOW with end-to-end flow and persistence touchpoints"
```

---

### Task 5: Author `14_DECISION_PIPELINE.md`

**Files:**
- Create: `docs/14_DECISION_PIPELINE.md`

**Interfaces:**
- Consumes: pipeline definition from `04`/`01`; `FR-DEC-01..03`, `FR-RISK-01`, `FR-EXEC-01`; `INV-04`; states from `06`; events from `07`.
- Produces: the per-stage gate definitions referenced by `32_RISK_ENGINE`, `33_STRATEGY_ENGINE`, `38_DECISION_ENGINE`. Clause prefix `PIPE-`.

- [ ] **Step 1: Write the document**

Create `docs/14_DECISION_PIPELINE.md` with exactly this structure and content:

````markdown
# Decision Pipeline

> **Question this document answers:** How does a market event become an outcome?

This document defines the mandatory business pipeline — the ordered stages every
piece of intelligence passes through, and the gate at each stage. It is the
*business* companion to `13_DATA_FLOW.md` (which describes physical movement) and
the structural sibling of `05_DOMAIN_MODEL.md` (whose entity chain this mirrors).

---

## Stages

```
Market Event → Feature → Insight → Recommendation → Decision → Execution → Outcome → Learning
```

| Stage | Input | Transformation | Gate |
|-------|-------|----------------|------|
| Feature | Market Event | standardized computation | computed exactly once |
| Insight | Feature | engine analysis | one domain per engine |
| Recommendation | Insight(s) | combine + explain | must carry action/confidence/reasons/warnings/evidence/horizon |
| Decision | Recommendation | selection/sanction | exactly one Recommendation |
| Execution | Decision | route to venue | **Risk approval required**; optional and off by default |
| Outcome | Execution | realize result | recorded with audit trail |
| Learning | Outcome | persist knowledge | full chain retained |

- **PIPE-01** The pipeline MUST progress in order: Market Event → Feature → Insight → Recommendation → Decision → Execution → Outcome → Learning (`FR-DEC-01`).
- **PIPE-02** The pipeline MUST NOT jump from analysis directly to execution; every intermediate stage MUST be produced (`FR-DEC-02`).
- **PIPE-03** Every Decision MUST pass the Risk Engine before any Execution (`INV-04`, `FR-RISK-01`).
- **PIPE-04** Each stage MUST be independently observable and testable (`FR-DEC-03`).
- **PIPE-05** Execution MUST be optional and disabled by default; with execution disabled the pipeline MUST still run through Decision (`FR-EXEC-01`, `NFR-QA-01`).
- **PIPE-06** Each stage transition MUST correspond to a domain event (`07_DOMAIN_EVENTS.md`) and, where the entity has a lifecycle, a state transition (`06_STATE_MACHINE.md`).
- **PIPE-07** Every recommendation reaching the Decision stage MUST be explainable (`FR-EXP-01`); an unexplainable candidate MUST NOT advance.

---

## Related Documents

- `05_DOMAIN_MODEL.md` — the entity chain this pipeline mirrors
- `06_STATE_MACHINE.md` — lifecycles advanced by these stages
- `13_DATA_FLOW.md` — the physical movement behind these stages
- `32_RISK_ENGINE.md` — the gate at the Execution boundary
- `38_DECISION_ENGINE.md` — how a Recommendation becomes a Decision
````

- [ ] **Step 2: Verify structure and clause count**

Run: `f=docs/14_DECISION_PIPELINE.md; grep -q "Question this document answers" $f && grep -q "## Related Documents" $f && grep -cE "\*\*PIPE-[0-9]" $f`
Expected: no error, final number is `7`.

- [ ] **Step 3: Verify no duplicate ID definitions**

Run: `grep -oE "\*\*PIPE-[0-9]+\*\*" docs/14_DECISION_PIPELINE.md | sort | uniq -d`
Expected: empty output.

- [ ] **Step 4: Commit**

```bash
git add docs/14_DECISION_PIPELINE.md && git commit -m "docs: add 14_DECISION_PIPELINE with stages and per-stage gates"
```

---

### Task 6: Author `15_EVENT_BUS.md`

**Files:**
- Create: `docs/15_EVENT_BUS.md`

**Interfaces:**
- Consumes: event catalog and guarantees from `07` (`DEVT-07`, `DEVT-08`); `INV-08`; Redis transport decision (spec §1, ADR-001).
- Produces: the transport contract (envelope format, subscription model) referenced by `42_EVENT_STORAGE` and `44_REDIS`. Clause prefix `EVT-`.

- [ ] **Step 1: Write the document**

Create `docs/15_EVENT_BUS.md` with exactly this structure and content:

````markdown
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
````

- [ ] **Step 2: Verify structure and clause count**

Run: `f=docs/15_EVENT_BUS.md; grep -q "Question this document answers" $f && grep -q "## Related Documents" $f && grep -cE "\*\*EVT-[0-9]" $f`
Expected: no error, final number is `8`.

- [ ] **Step 3: Verify no duplicate ID definitions and no `DEVT` collision**

Run: `grep -oE "\*\*EVT-[0-9]+\*\*" docs/15_EVENT_BUS.md | sort | uniq -d; grep -cE "\*\*DEVT-" docs/15_EVENT_BUS.md`
Expected: empty first line (no duplicates), then `0` (no `DEVT-` clause defined here; only referenced inline).

- [ ] **Step 4: Commit**

```bash
git add docs/15_EVENT_BUS.md && git commit -m "docs: add 15_EVENT_BUS with envelope, delivery guarantees, and subscription model"
```

---

### Task 7: Author `16_CONFIGURATION.md`

**Files:**
- Create: `docs/16_CONFIGURATION.md`

**Interfaces:**
- Consumes: philosophy §10; `NFR-CFG-01`, `NFR-SEC-01`, `NFR-SEC-02`; `FR-EXEC-01`.
- Produces: the configuration-vs-secrets boundary referenced by `64_SECURITY`. Clause prefix `CFG-` (distinct from `NFR-CFG-` in `08`).

- [ ] **Step 1: Write the document**

Create `docs/16_CONFIGURATION.md` with exactly this structure and content:

````markdown
# Configuration

> **Question this document answers:** How is behavior configured rather than hardcoded?

This document defines what the platform expresses as configuration and how
configuration is separated from secrets and from code. It operationalizes
`01_PRODUCT_PHILOSOPHY.md` §10 ("Configuration Over Hardcoding") and feeds the
security boundary detailed in `64_SECURITY.md`.

---

## What Is Configuration

| Category | Examples | Why configurable |
|----------|----------|------------------|
| Market/provider settings | enabled providers, endpoints, symbols | varies per deployment |
| Engine enablement | which Analysis Engines are active | pluggability (`FR-ANL-02`) |
| Thresholds & limits | risk limits, confidence cutoffs | tuning without redeploy |
| Execution gating | execution on/off, paper vs live | safety (`FR-EXEC-01`) |
| Interface selection | Telegram/web/REST/WS enablement | replaceable interfaces (`FR-IFC-01`) |

- **CFG-01** Behavior that varies by market, provider, or deployment MUST be expressed as configuration, not as code branches (`NFR-CFG-01`).
- **CFG-02** Effective configuration MUST be observable at runtime (`01_PRODUCT_PHILOSOPHY.md` §10).
- **CFG-03** The set of enabled engines, thresholds, limits, and provider settings MUST be configurable without recompilation.
- **CFG-04** Execution MUST be gated by explicit configuration and disabled by default (`FR-EXEC-01`, `NFR-SEC-02`).
- **CFG-05** Secrets (credentials, API keys) MUST NOT be stored in configuration committed to source; they MUST be injected at runtime from a secret source (`NFR-SEC-01`, `64_SECURITY.md`).
- **CFG-06** Configuration MUST be validated at startup; an invalid configuration MUST fail fast rather than run with unsafe defaults.

## Configuration vs Secrets

```
Configuration  → declarative, may be versioned, observable at runtime
Secrets        → injected at runtime, never committed, never logged
```

- **CFG-07** A configuration value that selects an unsafe capability (e.g. live execution) MUST require an explicit, non-default setting (`NFR-SEC-02`).

---

## Related Documents

- `01_PRODUCT_PHILOSOPHY.md` — §10, configuration over hardcoding
- `08_NON_FUNCTIONAL_REQUIREMENTS.md` — `NFR-CFG-*`, `NFR-SEC-*`
- `37_LIVE_TRADING.md` — the most safety-critical gated capability
- `64_SECURITY.md` — how secrets are protected and injected
````

- [ ] **Step 2: Verify structure and clause count**

Run: `f=docs/16_CONFIGURATION.md; grep -q "Question this document answers" $f && grep -q "## Related Documents" $f && grep -cE "\*\*CFG-[0-9]" $f`
Expected: no error, final number is `7`.

- [ ] **Step 3: Verify no duplicate ID definitions and no `NFR-CFG` collision**

Run: `grep -oE "\*\*CFG-[0-9]+\*\*" docs/16_CONFIGURATION.md | sort | uniq -d; grep -c "NFR-CFG" docs/16_CONFIGURATION.md`
Expected: empty first line (no duplicates), then a non-zero count is acceptable ONLY as inline references; confirm there is no line matching `^\s*-\s+\*\*NFR-CFG`.

Run: `grep -cE "^\s*-\s+\*\*NFR-CFG" docs/16_CONFIGURATION.md`
Expected: `0`.

- [ ] **Step 4: Commit**

```bash
git add docs/16_CONFIGURATION.md && git commit -m "docs: add 16_CONFIGURATION with config categories and secrets boundary"
```

---

### Task 8: Author `17_DEPENDENCY_RULES.md`

**Files:**
- Create: `docs/17_DEPENDENCY_RULES.md`

**Interfaces:**
- Consumes: layers/`ARCH-03` from `10`; `DM-07`/`DM-08` from `05`; `INV-09` from `09`; module boundaries from `12`.
- Produces: the enforceable dependency directions that the build/static analysis checks. Clause prefix `DEP-`.

- [ ] **Step 1: Write the document**

Create `docs/17_DEPENDENCY_RULES.md` with exactly this structure and content:

````markdown
# Dependency Rules

> **Question this document answers:** Which dependency directions are allowed?

This document states the enforceable dependency rules of the platform — the
concrete, checkable form of the Clean dependency direction declared in
`10_ARCHITECTURE.md` (`ARCH-03`) and the bounded-context isolation declared in
`05_DOMAIN_MODEL.md` (`DM-07`). A violation of any rule here is a build defect.

---

## Allowed Directions

```
Interface ─┐
           ├─▶ Application ─▶ Domain
Infrastructure ─┘                 ▲
   (adapters, persistence, AI) ───┘ via ports only

Forbidden: any arrow pointing OUT of Domain, or INTO business logic from a provider SDK.
```

- **DEP-01** The `Domain` layer MUST NOT depend on `Application`, `Infrastructure`, or `Interface`.
- **DEP-02** The `Application` layer MUST depend only on `Domain` abstractions.
- **DEP-03** `Infrastructure` and `Interface` MUST depend inward (on `Application`/`Domain` abstractions), never the reverse.
- **DEP-04** Business logic MUST NOT import any provider or venue SDK; only `market-adapter` modules may (`INV-09`, `FR-CONN-02`).
- **DEP-05** A bounded context MUST NOT depend on another context's internals; cross-context interaction MUST be via domain events or a published interface (`DM-07`).
- **DEP-06** The `Decision` context MUST NOT depend on any specific provider or venue; only the `Execution` context touches venues, via adapters (`DM-08`).
- **DEP-07** Dependency violations MUST be detectable by static analysis or a build-time check; the build SHOULD fail on a violation.
- **DEP-08** The `ai-gateway` module MUST be the only inbound dependency on external AI providers; no business module may depend on an AI SDK directly (`FR-AI-03`, `INV-06`).

## Enforcement

| Mechanism | Enforces |
|-----------|----------|
| Layer import boundaries (build check) | `DEP-01`, `DEP-02`, `DEP-03` |
| Adapter-only SDK imports | `DEP-04`, `DEP-08` |
| Context package boundaries | `DEP-05`, `DEP-06` |

---

## Related Documents

- `10_ARCHITECTURE.md` — `ARCH-03`, the dependency direction these rules enforce
- `05_DOMAIN_MODEL.md` — the bounded contexts isolated by `DEP-05`/`DEP-06`
- `12_MODULES.md` — the modules these rules constrain
- `60_ENGINEERING_PRINCIPLES.md` — how these rules are applied in practice
````

- [ ] **Step 2: Verify structure and clause count**

Run: `f=docs/17_DEPENDENCY_RULES.md; grep -q "Question this document answers" $f && grep -q "## Related Documents" $f && grep -cE "\*\*DEP-[0-9]" $f`
Expected: no error, final number is `8`.

- [ ] **Step 3: Verify no duplicate ID definitions**

Run: `grep -oE "\*\*DEP-[0-9]+\*\*" docs/17_DEPENDENCY_RULES.md | sort | uniq -d`
Expected: empty output.

- [ ] **Step 4: Commit**

```bash
git add docs/17_DEPENDENCY_RULES.md && git commit -m "docs: add 17_DEPENDENCY_RULES with allowed directions and enforcement"
```

---

### Task 9: Group 10 consistency pass

**Files:**
- Read-only verification across `docs/10`–`docs/17` (plus links into `00`–`09`).

**Interfaces:**
- Consumes: all eight Group 10 documents.
- Produces: a clean bill of health (or a fix list) before later stages build on the architecture baseline.

- [ ] **Step 1: Verify every Group 10 file has the required template elements**

Run:
```bash
for f in docs/10_ARCHITECTURE.md docs/11_SYSTEM_OVERVIEW.md docs/12_MODULES.md docs/13_DATA_FLOW.md docs/14_DECISION_PIPELINE.md docs/15_EVENT_BUS.md docs/16_CONFIGURATION.md docs/17_DEPENDENCY_RULES.md; do grep -q "Question this document answers" "$f" && grep -q "## Related Documents" "$f" && echo "OK $f" || echo "FAIL $f"; done
```
Expected: eight `OK` lines.

- [ ] **Step 2: Verify normative ID definitions are unique within each prefix**

Run:
```bash
for p in ARCH SYS MOD FLOW PIPE EVT CFG DEP; do dup=$(grep -rhoE "\*\*${p}-[0-9]+\*\*" docs/1[0-7]_*.md | sort | uniq -d); [ -z "$dup" ] && echo "$p unique" || echo "$p DUPLICATES: $dup"; done
```
Expected: each prefix prints "unique".

- [ ] **Step 3: Verify all intra-corpus links in Group 10 point to existing-or-planned files**

Run:
```bash
cd docs && for f in $(grep -ohE "[0-9]{2}_[A-Z_]+\.md" 1[0-7]_*.md | sort -u); do test -f "$f" || echo "not-yet-created: $f"; done; cd ..
```
Expected: only files numbered `20` and above, plus not-yet-created `40`-group and `60`-group files (e.g. `22_FEATURE_STORE.md`, `42_EVENT_STORAGE.md`, `44_REDIS.md`, `60_ENGINEERING_PRINCIPLES.md`, `64_SECURITY.md`) appear; no `00`–`17` file appears as missing.

- [ ] **Step 4: Verify no contradiction with locked decisions, invariants, NFRs, glossary**

Read `docs/10`–`docs/17` once end to end. Confirm:
- `ARCH-01`/`SYS-07`/`EVT-05` agree on modular monolith + Redis transport (spec §1, `ADR-001`).
- `SYS-03`/`PIPE-03`/`DEP-06` agree that Risk gates every execution path (`INV-04`).
- `SYS-04`/`FLOW-04` agree the Feature Store is the sole Feature source (`INV-02`).
- `SYS-06`/`DEP-04`/`DEP-08` agree only adapters/gateway touch providers (`INV-09`, `INV-06`).
- `ARCH-07`/`SYS-08`/`PIPE-05` agree the platform runs with AI/execution disabled (`INV-10`, `NFR-QA-01`).
- All terms used appear in `04_GLOSSARY.md`.

Expected: no contradictions. If any found, fix in the owning file and re-run Steps 1–3.

- [ ] **Step 5: Commit any fixes**

```bash
git add -A && git commit -m "docs: group 10 architecture consistency pass" || echo "no fixes needed"
```

---

## Self-Review Notes

- **Spec coverage:** This plan covers spec Production-Order step 3 (Stage 1, group 10) and the full Group 10 inventory (`10`–`17`) from spec §3 with the exact prefixes `ARCH-`/`SYS-`/`MOD-`/`FLOW-`/`PIPE-`/`EVT-`/`CFG-`/`DEP-`. Steps 4–13 (groups 60/40/30/20/50, ADRs, final corpus consistency pass, `docs.md` update) remain for subsequent per-stage plans.
- **Placeholder scan:** no `TBD`/`TODO`/"implement later"; every document body is provided verbatim.
- **ID consistency:** prefixes match the spec inventory; `MOD-`/`CFG-`/`EVT-` are explicitly disambiguated from the Foundation `NFR-MOD-`/`NFR-CFG-`/`DEVT-` prefixes by Step-3 collision checks in Tasks 3, 6, and 7.
- **Forward-reference policy:** Group 10 docs link forward to not-yet-created files (`20`+, `40`+, `60`+) per the spec's flat inventory; Task 9 Step 3 confirms no *backward* link (`00`–`17`) is broken.
```
