# Phase A + Foundation Documentation Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Relocate the existing Stage 0 documents into `docs/` and author the five Foundation documents (`05_DOMAIN_MODEL`, `06_STATE_MACHINE`, `07_DOMAIN_EVENTS`, `08_NON_FUNCTIONAL_REQUIREMENTS`, `09_SYSTEM_INVARIANTS`) that anchor vocabulary, laws, and quality targets for the whole corpus.

**Architecture:** Documentation-only effort. Each document follows the Documentation Style Contract from the governing spec (`docs/superpowers/specs/2026-06-26-platform-documentation-design.md`). There is no code; the "test" for each task is a verification pass (grep-based structural checks + manual content read) confirming the document satisfies the Style Contract and does not contradict locked decisions, the glossary, or the system invariants.

**Tech Stack:** Markdown (CommonMark + GitHub-flavored tables), ASCII diagrams. No external tooling, no renderers, no code listings.

## Global Constraints

These apply to EVERY task in this plan, copied from the spec's Style Contract (§2) and Locked Decisions (§1):

- **Flat layout:** all documents live directly under `docs/` — no subfolders. Existing numeric prefixes (`00`–`04`) are retained; no renumbering.
- **Document template (every file):** H1 title → blockquote `> **Question this document answers:** <single question>` → 1–2 paragraph purpose → `---` → topic sections → `---` → `## Related Documents` block.
- **One question per document.** Never mix architecture, requirements, and implementation.
- **Normative language** (MUST / MUST NOT / SHOULD / MAY) only in requirements/rules, not descriptive prose.
- **Stable IDs** for every normative clause, using the per-document prefix assigned in the spec inventory (e.g. `DM-01`, `STATE-01`, `DEVT-01`, `NFR-01`, `INV-01`). IDs are unique within the corpus.
- **Vocabulary from `04_GLOSSARY` only.** A new term must be added to the glossary first.
- **Language-neutral contracts:** entities/events/fields as tables or YAML-like blocks; no Go/Python in document bodies. The stack (Go core, optional Python ML, PostgreSQL+TimescaleDB, Redis) appears only as a named decision, never as code.
- **Diagrams are ASCII** (e.g. `Data → Features → …`).
- **Every document ends** with a `## Related Documents` cross-reference block whose links resolve to files that exist (or are scheduled in the spec inventory).
- **Must not contradict** a locked decision (spec §1), the glossary, or the system invariants (`09`).

---

## File Structure

| File | Responsibility |
|------|----------------|
| `docs/00_VISION.md` … `docs/04_GLOSSARY.md` | Relocated Stage 0 (moved, content unchanged except `docs/` path fixes). |
| `docs/05_DOMAIN_MODEL.md` | Core domain entities, their relationships, and bounded contexts. Prefix `DM-`. |
| `docs/06_STATE_MACHINE.md` | Entity lifecycles and legal transitions (Recommendation, Order, Position). Prefix `STATE-`. |
| `docs/07_DOMAIN_EVENTS.md` | Catalog of domain events: publisher, subscribers, payload, guarantees, idempotency. Prefix `DEVT-`. |
| `docs/08_NON_FUNCTIONAL_REQUIREMENTS.md` | Quality attributes with target thresholds; the sole home of NFRs. Prefix `NFR-`. |
| `docs/09_SYSTEM_INVARIANTS.md` | The platform's inviolable laws. Prefix `INV-`. |
| `docs/02_REQUIREMENTS.md` | Modified: existing NFR section reduced to a pointer to `08`. |

Order matters: `05` defines entities that `06`/`07` reference; `08`/`09` reference all of them. Stage 0 must move first so links resolve.

---

### Task 1: Relocate Stage 0 into `docs/`

**Files:**
- Move: `00_VISION.md`, `01_PRODUCT_PHILOSOPHY.md`, `02_REQUIREMENTS.md`, `03_ROADMAP.md`, `04_GLOSSARY.md` → `docs/`
- Move: `PROJECT_CONTEXT.md`, `docs.md` stay at repo root (they are project-level, not part of the `docs/` corpus)

**Interfaces:**
- Consumes: nothing.
- Produces: the `docs/00_VISION.md` … `docs/04_GLOSSARY.md` paths that every later task links to.

- [ ] **Step 1: Move the five Stage 0 files with git**

```bash
git mv 00_VISION.md 01_PRODUCT_PHILOSOPHY.md 02_REQUIREMENTS.md 03_ROADMAP.md 04_GLOSSARY.md docs/
```

- [ ] **Step 2: Verify the move and that no broken `docs/`-prefixed self-references were introduced**

Run: `ls docs/*.md && grep -rn "project_context.md" docs/`
Expected: the five files listed under `docs/`. The `00_VISION.md` reference to `project_context.md` is a root-level file pointer; leave it (it correctly points at repo root). No code references to fix because content already used bare filenames (e.g. `01_PRODUCT_PHILOSOPHY.md`), which resolve within `docs/`.

- [ ] **Step 3: Verify intra-corpus links still resolve**

Run: `cd docs && for f in $(grep -ohE "[0-9]{2}_[A-Z_]+\.md" *.md | sort -u); do test -f "$f" || echo "MISSING: $f"; done; cd ..`
Expected: only `05_`–`09_` and higher-numbered files (not yet created) print as MISSING; no Stage 0 file (`00`–`04`) is missing.

- [ ] **Step 4: Commit**

```bash
git add -A && git commit -m "docs: relocate Stage 0 documents into docs/"
```

---

### Task 2: Author `05_DOMAIN_MODEL.md`

**Files:**
- Create: `docs/05_DOMAIN_MODEL.md`

**Interfaces:**
- Consumes: terms from `docs/04_GLOSSARY.md` (Market, Instrument, Market Event, Feature, Insight, Recommendation, Decision, Execution, Position, Outcome, Learning Record).
- Produces: the canonical entity names and bounded-context names (`Market`, `Analysis`, `Decision`, `Execution`, `Infrastructure`) that `06`, `07`, `08`, `09` and all later docs reference. Entity ID prefix `DM-`.

- [ ] **Step 1: Write the document**

Create `docs/05_DOMAIN_MODEL.md` with exactly this structure and content:

```markdown
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
```

- [ ] **Step 2: Verify structure against the Style Contract**

Run: `f=docs/05_DOMAIN_MODEL.md; grep -q "^# Domain Model" $f && grep -q "Question this document answers" $f && grep -q "## Related Documents" $f && grep -cE "^\s*-\s+\*\*DM-[0-9]" $f`
Expected: no error, and the final number (count of `DM-` clauses) is `8`.

- [ ] **Step 3: Verify no duplicate IDs and all referenced terms exist in glossary**

Run: `grep -oE "DM-[0-9]+" docs/05_DOMAIN_MODEL.md | sort | uniq -d`
Expected: empty output (no duplicates).

- [ ] **Step 4: Commit**

```bash
git add docs/05_DOMAIN_MODEL.md && git commit -m "docs: add 05_DOMAIN_MODEL with entity graph and bounded contexts"
```

---

### Task 3: Author `06_STATE_MACHINE.md`

**Files:**
- Create: `docs/06_STATE_MACHINE.md`

**Interfaces:**
- Consumes: entity names from `docs/05_DOMAIN_MODEL.md` (Recommendation, Decision, Execution/Order, Position).
- Produces: the canonical state names used by `07_DOMAIN_EVENTS` (e.g. `Approved`, `Rejected`, `Filled`, `Closed`) and later by `32_RISK_ENGINE`/`31_EXECUTION_LAYER`. Clause prefix `STATE-`.

- [ ] **Step 1: Write the document**

Create `docs/06_STATE_MACHINE.md` with exactly this structure and content:

```markdown
# State Machines

> **Question this document answers:** What lifecycle states do entities move through, and what transitions are legal?

This document defines the lifecycle state machines for the entities that change
state over time. It complements `05_DOMAIN_MODEL.md` (which defines the entities)
by specifying their legal states and transitions. Illegal transitions are
defects; every state change is observable.

---

## Recommendation Lifecycle

```
Created → Validated → Approved
                   ↘ Rejected
```

- **STATE-01** A `Recommendation` MUST begin in `Created` and reach `Approved` only through `Validated`.
- **STATE-02** A `Recommendation` MUST move to `Rejected` from `Validated` when the Risk Engine vetoes it; `Rejected` is terminal.

## Order Lifecycle

```
Created → Submitted → Accepted → Filled → Closed
                   ↘ Rejected
```

- **STATE-03** An `Order` MUST NOT move to `Submitted` unless its originating `Decision` is in the `Approved` state (`STATE-01`).
- **STATE-04** `Filled` and `Closed` are the only success-terminal states; `Rejected` is the failure-terminal state.

## Position Lifecycle

```
Opening → Opened → Closing → Closed
```

- **STATE-05** A `Position` MUST progress monotonically through `Opening → Opened → Closing → Closed`; no state may be skipped.
- **STATE-06** A `Position` MUST reach `Opened` only after at least one `Order` reached `Filled`.

## Transition Rules

- **STATE-07** Every state transition MUST emit a corresponding domain event (see `07_DOMAIN_EVENTS.md`).
- **STATE-08** Every state transition MUST be auditable after the fact, recording prior state, new state, and cause.

---

## Related Documents

- `05_DOMAIN_MODEL.md` — the entities whose lifecycles are defined here
- `07_DOMAIN_EVENTS.md` — events emitted on each transition
- `32_RISK_ENGINE.md` — the component that drives Recommendation to Rejected
- `31_EXECUTION_LAYER.md` — the component that drives the Order lifecycle
```

- [ ] **Step 2: Verify structure and clause count**

Run: `f=docs/06_STATE_MACHINE.md; grep -q "Question this document answers" $f && grep -q "## Related Documents" $f && grep -cE "\*\*STATE-[0-9]" $f`
Expected: no error, final number is `8`.

- [ ] **Step 3: Verify no duplicate IDs**

Run: `grep -oE "STATE-[0-9]+" docs/06_STATE_MACHINE.md | sort | uniq -d`
Expected: empty output.

- [ ] **Step 4: Commit**

```bash
git add docs/06_STATE_MACHINE.md && git commit -m "docs: add 06_STATE_MACHINE with recommendation/order/position lifecycles"
```

---

### Task 4: Author `07_DOMAIN_EVENTS.md`

**Files:**
- Create: `docs/07_DOMAIN_EVENTS.md`

**Interfaces:**
- Consumes: entity names from `05`, state names from `06`.
- Produces: the canonical domain-event names (e.g. `RecommendationCreated`, `DecisionApproved`, `OrderFilled`, `PositionClosed`) referenced by `15_EVENT_BUS` and `42_EVENT_STORAGE`. Clause prefix `DEVT-`.

- [ ] **Step 1: Write the document**

Create `docs/07_DOMAIN_EVENTS.md` with exactly this structure and content:

```markdown
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
```

- [ ] **Step 2: Verify structure and clause count**

Run: `f=docs/07_DOMAIN_EVENTS.md; grep -q "Question this document answers" $f && grep -q "## Related Documents" $f && grep -cE "\*\*DEVT-[0-9]" $f`
Expected: no error, final number is `8`.

- [ ] **Step 3: Verify no duplicate IDs**

Run: `grep -oE "DEVT-[0-9]+" docs/07_DOMAIN_EVENTS.md | sort | uniq -d`
Expected: empty output.

- [ ] **Step 4: Commit**

```bash
git add docs/07_DOMAIN_EVENTS.md && git commit -m "docs: add 07_DOMAIN_EVENTS catalog with publishers, payloads, and guarantees"
```

---

### Task 5: Author `08_NON_FUNCTIONAL_REQUIREMENTS.md` and repoint `02`

**Files:**
- Create: `docs/08_NON_FUNCTIONAL_REQUIREMENTS.md`
- Modify: `docs/02_REQUIREMENTS.md` (replace the body of section 3 "Non-Functional Requirements" with a pointer to `08`)

**Interfaces:**
- Consumes: the existing `NFR-ARCH-*`, `NFR-MOD-*`, `NFR-OBS-*`, `NFR-CFG-*`, `NFR-TEST-*`, `NFR-SEC-*` IDs currently in `02_REQUIREMENTS.md` (they migrate here unchanged to preserve cross-references).
- Produces: `08` as the single home of NFRs. Clause prefix `NFR-` (existing sub-prefixes retained; new quality-attribute clauses use `NFR-QA-`).

- [ ] **Step 1: Read the current NFR section of `02` to migrate IDs verbatim**

Run: `sed -n '/## 3. Non-Functional/,/## 4. Out of Scope/p' docs/02_REQUIREMENTS.md`
Expected: prints the existing NFR clauses (`NFR-ARCH-01` … `NFR-SEC-02`). Keep this output to paste into `08`.

- [ ] **Step 2: Create `docs/08_NON_FUNCTIONAL_REQUIREMENTS.md`**

Create the file with this structure. Paste the migrated clauses from Step 1 verbatim under "Migrated Architecture/Quality NFRs", then add the quality-attribute thresholds:

```markdown
# Non-Functional Requirements

> **Question this document answers:** What are the system's quality attributes and their target thresholds?

This document is the single home for the platform's non-functional requirements.
It consolidates the NFRs previously embedded in `02_REQUIREMENTS.md` and adds
quality-attribute targets. Functional requirements remain in `02_REQUIREMENTS.md`.

---

## Quality Attributes and Targets

| Attribute | Target | Notes |
|-----------|--------|-------|
| Availability | 99.9% for analysis path | execution path may be intentionally halted by Risk |
| Latency | feature compute p99 < 250 ms from event receipt | hot path served via Redis |
| Scalability | horizontal by instrument partition | modular monolith first, seams per bounded context |
| Reliability | no lost domain events | at-least-once + idempotent consumers (`07`) |
| Fault tolerance | degrade, never bypass Risk | a failed engine drops its insights, never its veto |
| Consistency | per-aggregate ordering | global ordering not required |
| Security | secrets never in business logic | see `64_SECURITY.md` |
| Recovery | event replay from store | deterministic rebuild via `42_EVENT_STORAGE.md` |
| Observability | every pipeline stage traceable | see `65_OBSERVABILITY.md` |

- **NFR-QA-01** The analysis path MUST remain available even when the execution path is disabled.
- **NFR-QA-02** A failure in any single Analysis Engine MUST NOT prevent other engines from producing insights, and MUST NOT weaken the Risk Engine veto.
- **NFR-QA-03** The platform MUST be able to deterministically rebuild derived state by replaying persisted domain events.

## Migrated Architecture and Quality NFRs

The following clauses were relocated verbatim from `02_REQUIREMENTS.md`; their
identifiers are unchanged so existing cross-references remain valid.

<!-- Paste NFR-ARCH-01 … NFR-SEC-02 from Step 1 here, unchanged -->

---

## Related Documents

- `02_REQUIREMENTS.md` — functional requirements; its NFR section points here
- `09_SYSTEM_INVARIANTS.md` — laws that some NFRs operationalize
- `64_SECURITY.md` — security NFRs in detail
- `65_OBSERVABILITY.md` — observability NFRs in detail
```

When pasting in Step 1's output, convert the existing `### 3.x` subsection headers to `### ` headers (drop the `3.` numbering) and keep every `**NFR-...-NN**` clause exactly as written.

- [ ] **Step 3: Replace section 3 of `docs/02_REQUIREMENTS.md` with a pointer**

Replace the entire block from `## 3. Non-Functional Requirements` up to (but not including) `## 4. Out of Scope` with:

```markdown
## 3. Non-Functional Requirements

Non-functional requirements have moved to their single home,
`08_NON_FUNCTIONAL_REQUIREMENTS.md`. The identifiers (`NFR-ARCH-*`, `NFR-MOD-*`,
`NFR-OBS-*`, `NFR-CFG-*`, `NFR-TEST-*`, `NFR-SEC-*`) are preserved there, so
existing cross-references remain valid.
```

- [ ] **Step 4: Verify the migration preserved every NFR id**

Run: `for id in NFR-ARCH-01 NFR-ARCH-02 NFR-ARCH-03 NFR-ARCH-04 NFR-MOD-01 NFR-MOD-02 NFR-OBS-01 NFR-OBS-02 NFR-CFG-01 NFR-TEST-01 NFR-TEST-02 NFR-SEC-01 NFR-SEC-02; do grep -q "$id" docs/08_NON_FUNCTIONAL_REQUIREMENTS.md || echo "MISSING $id"; done`
Expected: empty output (every legacy id present in `08`).

- [ ] **Step 5: Verify `02` no longer defines NFR bodies and `08` structure is valid**

Run: `grep -c "NFR-ARCH-01" docs/02_REQUIREMENTS.md; f=docs/08_NON_FUNCTIONAL_REQUIREMENTS.md; grep -q "Question this document answers" $f && grep -q "## Related Documents" $f && echo OK`
Expected: first number is `0` (no NFR clause left in `02`), then `OK`.

- [ ] **Step 6: Commit**

```bash
git add docs/08_NON_FUNCTIONAL_REQUIREMENTS.md docs/02_REQUIREMENTS.md && git commit -m "docs: add 08_NON_FUNCTIONAL_REQUIREMENTS as sole NFR home; repoint 02"
```

---

### Task 6: Author `09_SYSTEM_INVARIANTS.md`

**Files:**
- Create: `docs/09_SYSTEM_INVARIANTS.md`

**Interfaces:**
- Consumes: entities (`05`), events (`07`), and the locked decisions from the spec.
- Produces: the invariant IDs (`INV-`) that enforcing components (Risk Engine, Feature Store, AI Gateway) reference. This is the corpus's law sheet.

- [ ] **Step 1: Write the document**

Create `docs/09_SYSTEM_INVARIANTS.md` with exactly this structure and content:

```markdown
# System Invariants

> **Question this document answers:** What laws must always hold and may never be violated?

This document states the platform's invariants — the laws that every component,
in every state, must uphold. They are neither functional requirements nor
architecture; they are non-negotiable constraints. Any change to an invariant
requires a superseding ADR. The component that enforces each invariant is named
so the law has an owner.

---

## Invariants

- **INV-01** Every `Feature` has exactly one producer. *(Enforced by: Feature Engine, `21_FEATURE_ENGINE.md`.)*
- **INV-02** The Feature Store is the only source of Features; no subsystem computes its own. *(Enforced by: Feature Store, `22_FEATURE_STORE.md`.)*
- **INV-03** Every `Recommendation` maps to at most one `Decision`. *(Enforced by: Decision Engine, `38_DECISION_ENGINE.md`.)*
- **INV-04** Every `Decision` passes the Risk Engine before any execution. *(Enforced by: Risk Engine, `32_RISK_ENGINE.md`.)*
- **INV-05** The Risk Engine veto cannot be bypassed by any actor, human or AI. *(Enforced by: Risk Engine, `32_RISK_ENGINE.md`.)*
- **INV-06** AI never executes trades and never overrides Risk or business rules. *(Enforced by: AI Gateway, `23_AI_GATEWAY.md`.)*
- **INV-07** Every `Execution` has an audit record traceable to its originating `Market Event`. *(Enforced by: Execution Layer, `31_EXECUTION_LAYER.md`.)*
- **INV-08** Every domain event has exactly one publisher and is immutable. *(Enforced by: Event Bus, `15_EVENT_BUS.md`.)*
- **INV-09** Business logic never knows which provider or venue it operates on. *(Enforced by: adapters, `30_MARKET_ADAPTERS.md`.)*
- **INV-10** Removing AI entirely leaves a coherent, functioning platform. *(Enforced by: architecture, `10_ARCHITECTURE.md`.)*

## Status of Invariants

- **INV-11** These invariants MUST NOT be weakened by any feature; a conflicting feature is rejected by design.
- **INV-12** Any change to an invariant MUST be recorded as a superseding ADR (see Architecture Freeze, governing spec §5).

---

## Related Documents

- `01_PRODUCT_PHILOSOPHY.md` — the principles these laws operationalize
- `05_DOMAIN_MODEL.md` — the entities these laws constrain
- `08_NON_FUNCTIONAL_REQUIREMENTS.md` — quality targets some invariants support
- `32_RISK_ENGINE.md` — enforcer of the risk invariants (INV-04, INV-05)
```

- [ ] **Step 2: Verify structure and clause count**

Run: `f=docs/09_SYSTEM_INVARIANTS.md; grep -q "Question this document answers" $f && grep -q "## Related Documents" $f && grep -cE "\*\*INV-[0-9]" $f`
Expected: no error, final number is `12`.

- [ ] **Step 3: Verify no duplicate IDs and that each invariant names an enforcer**

Run: `grep -oE "INV-[0-9]+" docs/09_SYSTEM_INVARIANTS.md | sort | uniq -d; grep -cE "Enforced by" docs/09_SYSTEM_INVARIANTS.md`
Expected: empty first line (no duplicates), then `10` (each of INV-01..10 names an enforcer).

- [ ] **Step 4: Commit**

```bash
git add docs/09_SYSTEM_INVARIANTS.md && git commit -m "docs: add 09_SYSTEM_INVARIANTS with enforced platform laws"
```

---

### Task 7: Foundation consistency pass

**Files:**
- Read-only verification across `docs/00`–`docs/09`.

**Interfaces:**
- Consumes: all Foundation documents.
- Produces: a clean bill of health (or a fix list) before later stages build on this base.

- [ ] **Step 1: Verify every Foundation file has the required template elements**

Run: `for f in docs/05_DOMAIN_MODEL.md docs/06_STATE_MACHINE.md docs/07_DOMAIN_EVENTS.md docs/08_NON_FUNCTIONAL_REQUIREMENTS.md docs/09_SYSTEM_INVARIANTS.md; do grep -q "Question this document answers" "$f" && grep -q "## Related Documents" "$f" && echo "OK $f" || echo "FAIL $f"; done`
Expected: five `OK` lines.

- [ ] **Step 2: Verify normative IDs are unique within each document**

Run: `for p in DM STATE DEVT NFR-QA INV; do dup=$(grep -rhoE "${p}-[0-9]+" docs/0*.md | sort | uniq -d); [ -z "$dup" ] && echo "$p unique" || echo "$p DUPLICATES: $dup"; done`
Expected: each prefix prints "unique".

- [ ] **Step 3: Verify all intra-corpus links in Foundation docs point to existing-or-planned files**

Run: `cd docs && for f in $(grep -ohE "[0-9]{2}_[A-Z_]+\.md" 0[5-9]_*.md | sort -u); do test -f "$f" || echo "not-yet-created: $f"; done; cd ..`
Expected: only files numbered `10` and above (e.g. `14_DECISION_PIPELINE.md`, `21_FEATURE_ENGINE.md`, `32_RISK_ENGINE.md`) appear as "not-yet-created"; no `00`–`09` file appears.

- [ ] **Step 4: Manual read for contradiction**

Read `docs/05`–`docs/09` once end to end. Confirm: no document contradicts a locked decision (spec §1), the glossary, or an invariant. In particular confirm `INV-02` (Feature Store sole source) matches `01_PRODUCT_PHILOSOPHY.md` §5, and `STATE-03` (Order needs Approved Decision) matches `INV-04`.
Expected: no contradictions; if any found, fix in the owning file and re-run Steps 1–3.

- [ ] **Step 5: Commit any fixes**

```bash
git add -A && git commit -m "docs: foundation consistency pass for 05-09" || echo "no fixes needed"
```

---

## Self-Review Notes

- **Spec coverage:** This plan covers spec Production-Order steps 1 (relocate Stage 0) and 2 (Foundation `05`–`09`, repoint `02` NFR). Steps 3–13 (groups 10/40/60/30/20/50, ADRs, final consistency pass, `docs.md` update) are out of scope for this plan and will be covered by subsequent per-stage plans.
- **Placeholder scan:** the only intentional fill-in is the verbatim paste of existing `NFR-*` clauses in Task 5 Step 2, which is sourced deterministically from Step 1's command output — not an open-ended placeholder.
- **ID consistency:** prefixes used (`DM-`, `STATE-`, `DEVT-`, `NFR-QA-`, `INV-`) match the spec inventory; legacy `NFR-ARCH/MOD/OBS/CFG/TEST/SEC` retained unchanged.
