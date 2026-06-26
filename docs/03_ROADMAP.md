# Roadmap

> **Question this document answers:** In what order do we deliver the platform,
> and how is the work staged?

This roadmap sequences delivery into stages. Each stage is a coherent slice of
capability with explicit entry and exit criteria. Stages are ordered by
dependency, not by ambition: each stage exists to make the next one possible.

The roadmap governs both **documentation delivery** and **build delivery**; the
two share the same staging so that documentation always precedes the code it
describes.

---

## Staging Principles

- **Foundation before features.** Architecture, data, and engineering standards
  precede intelligence and trading.
- **Analysis before execution.** The platform must analyze and explain before it
  is ever allowed to act.
- **Risk before live capital.** Risk controls and paper trading precede any live
  execution.
- **A stage is done when its exit criteria are met** — not when its files exist.

---

## Stage 0 — Product Foundation

**Goal:** Establish shared intent and vocabulary.

**Documents:** `00_VISION`, `01_PRODUCT_PHILOSOPHY`, `02_REQUIREMENTS`,
`03_ROADMAP`, `04_GLOSSARY`.

**Exit criteria:**
- Vision, philosophy, and requirements are approved.
- Canonical vocabulary is defined and adopted across all later documents.

---

## Stage 1 — Architecture Baseline

**Goal:** Define how the platform is structured before any component is built.

**Documents (group 10):** `10_ARCHITECTURE`, `11_SYSTEM_OVERVIEW`, `12_MODULES`,
`13_DATA_FLOW`, `14_DECISION_PIPELINE`, `15_EVENT_BUS`, `16_CONFIGURATION`,
`17_DEPENDENCY_RULES`.

**Decisions (group 70):** `ADR-001` (Modular Monolith), `ADR-008` (Universal
Market Platform), `ADR-004` (Risk-First).

**Exit criteria:**
- Module boundaries, dependency rules, and the event bus contract are defined.
- The decision pipeline is specified end to end.
- Foundational ADRs are accepted.

---

## Stage 2 — Engineering and Data Foundations

**Goal:** Establish the standards and persistence the rest of the system relies on.

**Documents (group 60):** `60_ENGINEERING_PRINCIPLES`, `61_AI_DEVELOPMENT_RULES`,
`62_CODING_STYLE`, `63_TESTING`, `64_SECURITY`, `65_LOGGING`, `66_MONITORING`.

**Documents (group 40):** `40_DATABASE`, `41_REDIS`, `42_EVENT_STORAGE`,
`43_MARKET_STORAGE`.

**Exit criteria:**
- Coding, testing, security, logging, and monitoring standards are defined.
- Persistence model for events, market data, and history is specified.

---

## Stage 3 — Market Connectivity

**Goal:** Bring real, standardized market data into the platform.

**Documents (group 30, partial):** `30_MARKET_ADAPTERS`.

**Decisions (group 70):** `ADR-005` (Exchange Adapters).

**Exit criteria:**
- Adapter interface is defined; at least one provider is integrated end to end.
- Standardized market-data domain models flow into storage.

---

## Stage 4 — Feature and Intelligence Core

**Goal:** Turn standardized data into reusable features and explainable insight.

**Documents (group 20):** `20_INTELLIGENCE_CORE`, `21_FEATURE_ENGINE`,
`22_FEATURE_STORE`, `23_AI_GATEWAY`, `24_LLM_PROMPTS`, `25_PATTERN_ENGINE`,
`26_LEARNING_ENGINE`, `27_CONFIDENCE_ENGINE`.

**Decisions (group 70):** `ADR-002` (Feature Store), `ADR-003` (AI Gateway).

**Exit criteria:**
- Feature Store is the single source of truth; features are computed once.
- At least one analytical engine produces explainable insights.
- AI Gateway abstracts AI providers; platform functions with AI disabled.

---

## Stage 5 — Decision, Risk, and Strategy

**Goal:** Convert insights into governed decisions with absolute risk control.

**Documents (group 30, partial):** `32_RISK_ENGINE`, `33_STRATEGY_ENGINE`,
`34_PORTFOLIO_ENGINE`.

**Decisions (group 70):** `ADR-004` (Risk-First) confirmed in implementation.

**Exit criteria:**
- Risk Engine exercises veto over every decision.
- Strategy and portfolio logic operate on standardized domain models only.

---

## Stage 6 — Execution: Backtest and Paper

**Goal:** Validate the full pipeline without risking capital.

**Documents (group 30, partial):** `31_EXECUTION_LAYER`, `35_BACKTEST_ENGINE`,
`36_PAPER_TRADING`.

**Decisions (group 70):** `ADR-007` (Paper Trading).

**Exit criteria:**
- Backtesting reproduces decisions deterministically from history.
- Paper trading runs the same pipeline as live, with execution simulated.

---

## Stage 7 — Interfaces

**Goal:** Expose the platform's intelligence to users through replaceable surfaces.

**Documents (group 50):** `50_TELEGRAM_BOT`, `51_WEB_DASHBOARD`, `52_REST_API`,
`53_WEBSOCKET_API`.

**Decisions (group 70):** `ADR-006` (Telegram MVP).

**Exit criteria:**
- At least one interface (Telegram MVP) surfaces recommendations and status.
- Interfaces are replaceable without business-logic change.

---

## Stage 8 — Live Trading

**Goal:** Enable optional live execution under full risk governance.

**Documents (group 30, partial):** `37_LIVE_TRADING`.

**Exit criteria:**
- Live execution is opt-in, configuration-gated, and routed only through the Risk
  Engine.
- Every live action is auditable end to end.

---

## Stage Dependency Summary

```
Stage 0  Product Foundation
   ↓
Stage 1  Architecture Baseline
   ↓
Stage 2  Engineering + Data Foundations
   ↓
Stage 3  Market Connectivity
   ↓
Stage 4  Feature + Intelligence Core
   ↓
Stage 5  Decision + Risk + Strategy
   ↓
Stage 6  Backtest + Paper
   ↓
Stage 7  Interfaces
   ↓
Stage 8  Live Trading
```

---

## Related Documents

- `00_VISION.md` — what the product is and why it exists
- `02_REQUIREMENTS.md` — what each stage must satisfy
- `10_ARCHITECTURE.md` — structure delivered in Stage 1
- `70 ADR/` — architecture decisions accepted across stages
