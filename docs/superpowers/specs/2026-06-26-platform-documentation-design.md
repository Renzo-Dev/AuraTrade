# Platform Documentation — Design Spec

> **Question this document answers:** How will the remaining architecture
> documentation for the Universal Market Intelligence Platform be produced, so
> that ~48 documents read as a single, internally consistent body of work?

This spec governs the production of all documentation downstream of Stage 0
(`00_VISION` … `04_GLOSSARY`). It defines the style contract, the full document
inventory, the technology decisions encoded across documents, and the production
order. It is a meta-document: it describes how the docs are written, not the
platform itself.

---

## 1. Decisions Locked During Brainstorming

- **Scope:** produce all remaining documents (groups 10–70), ~43 new files plus
  one new ADR, plus relocation of Stage 0.
- **Location:** flat layout under `docs/` (no subfolders). Prefixes `10_`, `20_`
  … provide ordering and grouping. Stage 0 files move into `docs/` as well.
- **Depth:** conceptual prose + language-neutral contracts where useful (event
  schemas, message formats, REST/WS endpoint tables, DB column tables). No
  language-specific code listings in document bodies.
- **Stack:** Go-monolingual by default. ML Engine is an optional, pluggable
  component that lives behind a process boundary and *may* be Python; in-process
  ONNX inference is the documented alternative. Persistence: PostgreSQL +
  TimescaleDB; Redis for the event bus transport and hot-path cache. Removing the
  ML Engine (and AI entirely) must leave a coherent, functioning platform.
- **Production strategy:** linear, single-author, staged per `03_ROADMAP`
  (approach A). ADRs written last, once decisions are reflected in the docs.

---

## 2. Documentation Style Contract

Every document MUST follow this contract so the corpus reads as one author.

### 2.1 Document template

```
# <Title>

> **Question this document answers:** <single question>

<1–2 paragraphs: purpose of this document>

---

## <topic sections>

---

## Related Documents
- `XX_NAME.md` — <why a reader would go there>
```

### 2.2 Rules

- **One question per document.** Never mix architecture, requirements, and
  implementation in a single file.
- **Normative language** (MUST / MUST NOT / SHOULD / MAY) is reserved for
  requirements and rules, not for descriptive prose.
- **Stable identifiers** for normative clauses, following the `FR-CONN-01`
  pattern already established. Per-document prefixes are listed in §3.
- **Vocabulary comes from `04_GLOSSARY`.** Introducing a new term requires adding
  it to the glossary first.
- **Contracts are language-neutral:** event schemas and domain models as field
  tables or YAML-like blocks; REST/WS as endpoint/method/purpose tables; DB as
  column tables. No Go/Python in document bodies.
- **Diagrams are ASCII** (e.g. `Data → Features → …`); no external renderers.
- **Every document ends** with a Related Documents cross-reference block.
- **ADR format:** Status / Context / Decision / Consequences / Alternatives
  Considered.
- **Stack appears as a decision, not as code.** Go/Postgres/Redis are named where
  architecturally relevant, never as listings.

### 2.3 Consistency pass

After all documents are written, a final pass verifies: terms match the
glossary, normative IDs are unique, every Related Documents link resolves, and no
two documents contradict each other on a locked decision.

---

## 3. Document Inventory

Stage 0 (existing, to be relocated into `docs/`): `00_VISION`,
`01_PRODUCT_PHILOSOPHY`, `02_REQUIREMENTS`, `03_ROADMAP`, `04_GLOSSARY`.

### Group 10 — Architecture (ID prefixes in parentheses)

| File | Question it answers | Normative prefix |
|------|--------------------|------------------|
| `10_ARCHITECTURE.md` | What are the architectural styles and layers, and how do they combine? | `ARCH-` |
| `11_SYSTEM_OVERVIEW.md` | What are the runtime components and how do they fit together? | `SYS-` |
| `12_MODULES.md` | What modules exist and what is each responsible for? | `MOD-` |
| `13_DATA_FLOW.md` | How does data move from provider to learning? | `FLOW-` |
| `14_DECISION_PIPELINE.md` | How does a market event become an outcome? | `PIPE-` |
| `15_EVENT_BUS.md` | What is the event contract between modules? | `EVT-` |
| `16_CONFIGURATION.md` | How is behavior configured rather than hardcoded? | `CFG-` |
| `17_DEPENDENCY_RULES.md` | Which dependency directions are allowed? | `DEP-` |

### Group 20 — Intelligence

| File | Question it answers | Prefix |
|------|--------------------|--------|
| `20_INTELLIGENCE_CORE.md` | How is analysis federated across engines (no single core)? | `INT-` |
| `21_FEATURE_ENGINE.md` | How are market events turned into features? | `FENG-` |
| `22_FEATURE_STORE.md` | How are features stored as the single source of truth? | `FSTORE-` |
| `23_AI_GATEWAY.md` | How are AI providers abstracted and replaced? | `AIGW-` |
| `24_LLM_PROMPTS.md` | How are LLM prompts structured and governed? | `PROMPT-` |
| `25_PATTERN_ENGINE.md` | How does the pattern-recognition engine work? | `PAT-` |
| `26_LEARNING_ENGINE.md` | How are outcomes turned into reusable knowledge? | `LRN-` |
| `27_CONFIDENCE_ENGINE.md` | How is confidence quantified and attached? | `CONF-` |

### Group 30 — Trading

| File | Question it answers | Prefix |
|------|--------------------|--------|
| `30_MARKET_ADAPTERS.md` | What is the adapter contract for providers/markets? | `ADP-` |
| `31_EXECUTION_LAYER.md` | How are approved decisions routed to venues? | `EXEC-` |
| `32_RISK_ENGINE.md` | How does the Risk Engine exercise its veto? | `RISK-` |
| `33_STRATEGY_ENGINE.md` | How are insights turned into candidate decisions? | `STRAT-` |
| `34_PORTFOLIO_ENGINE.md` | How are positions and allocation managed? | `PORT-` |
| `35_BACKTEST_ENGINE.md` | How is history replayed deterministically? | `BT-` |
| `36_PAPER_TRADING.md` | How does paper trading mirror live? | `PAPER-` |
| `37_LIVE_TRADING.md` | How is live execution gated and audited? | `LIVE-` |

### Group 40 — Data

| File | Question it answers | Prefix |
|------|--------------------|--------|
| `40_DATABASE.md` | What is the relational/time-series persistence model? | `DB-` |
| `41_REDIS.md` | How is Redis used for transport and hot-path cache? | `RDS-` |
| `42_EVENT_STORAGE.md` | How are domain events persisted and replayed? | `ESTORE-` |
| `43_MARKET_STORAGE.md` | How is raw/standardized market data stored? | `MSTORE-` |

### Group 50 — Interfaces

| File | Question it answers | Prefix |
|------|--------------------|--------|
| `50_TELEGRAM_BOT.md` | How does the Telegram interface surface intelligence? | `TG-` |
| `51_WEB_DASHBOARD.md` | What does the web dashboard expose? | `WEB-` |
| `52_REST_API.md` | What is the REST contract? | `REST-` |
| `53_WEBSOCKET_API.md` | What is the streaming contract? | `WS-` |

### Group 60 — Engineering

| File | Question it answers | Prefix |
|------|--------------------|--------|
| `60_ENGINEERING_PRINCIPLES.md` | What engineering practices derive from the philosophy? | `ENG-` |
| `61_AI_DEVELOPMENT_RULES.md` | What rules constrain AI-assisted development? | `AIDEV-` |
| `62_CODING_STYLE.md` | What are the Go (and Python ML) style rules? | `STYLE-` |
| `63_TESTING.md` | What is the testing strategy and required coverage? | `TEST-` |
| `64_SECURITY.md` | How are secrets, credentials, and execution paths protected? | `SEC-` |
| `65_LOGGING.md` | What is logged and in what structure? | `LOG-` |
| `66_MONITORING.md` | What is monitored and alerted? | `MON-` |

### Group 70 — Architecture Decision Records

| File | Decision |
|------|----------|
| `ADR-001-Modular-Monolith.md` | Initial deployment is a modular monolith. |
| `ADR-002-Feature-Store.md` | Single Feature Store as source of truth. |
| `ADR-003-AI-Gateway.md` | AI providers abstracted behind a gateway. |
| `ADR-004-Risk-First.md` | Risk Engine holds absolute veto. |
| `ADR-005-Exchange-Adapters.md` | Providers integrated only via adapters. |
| `ADR-006-Telegram-MVP.md` | Telegram is the MVP interface. |
| `ADR-007-Paper-Trading.md` | Paper trading precedes live. |
| `ADR-008-Universal-Market-Platform.md` | Market-agnostic core. |
| `ADR-009-Language-And-Runtime.md` | **New.** Go-monolingual core; ML Engine optional behind a process boundary, may be Python; ONNX in-process is the alternative. |

**Total new files:** 8 (group 10) + 8 (20) + 8 (30) + 4 (40) + 4 (50) + 7 (60) +
9 (70 ADR) = **48 new files**, plus relocation of the 5 Stage 0 files.

---

## 4. Production Order

Documents are produced in roadmap-stage order so that each document can safely
reference earlier ones:

1. **Relocate Stage 0** into `docs/` and fix internal `docs/` references.
2. **Stage 1 (group 10)** — architecture baseline.
3. **Stage 2 (groups 60, then 40)** — engineering + data foundations.
4. **Stage 3 (`30_MARKET_ADAPTERS`)** — connectivity.
5. **Stage 4 (group 20)** — feature + intelligence core.
6. **Stage 5 (`32`, `33`, `34`)** — decision, risk, strategy.
7. **Stage 6 (`31`, `35`, `36`)** — backtest + paper.
8. **Stage 7 (group 50)** — interfaces.
9. **Stage 8 (`37`)** — live trading.
10. **ADRs (group 70)** — written last, reflecting decisions now in the docs.
11. **Consistency pass** — per §2.3.
12. **Update `docs.md`** to list `ADR-009` and reflect the flat `docs/` layout.

---

## 5. Out of Scope

- No source code, build scaffolding, or project skeleton — documentation only.
- No new product requirements beyond what Stage 0 already defines; downstream
  docs elaborate and structure existing requirements, they do not invent scope.
- No external diagram tooling or generated assets.

---

## 6. Success Criteria

- All 48 files exist under `docs/`, each following the style contract.
- Every normative clause has a unique, prefixed ID.
- Every Related Documents link resolves to an existing file.
- No document contradicts a locked decision (§1) or the glossary.
- `docs.md` reflects the final inventory including `ADR-009`.
