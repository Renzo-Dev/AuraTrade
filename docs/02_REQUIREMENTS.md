# Requirements

> **Question this document answers:** What must the platform do, and to what
> standard?

This document specifies functional and non-functional requirements. Requirements
are normative: keywords **MUST**, **MUST NOT**, **SHOULD**, and **MAY** carry
their conventional meaning. Each requirement has a stable identifier for
cross-referencing.

---

## 1. Scope

The platform ingests market data from arbitrary markets and providers, transforms
it into standardized intelligence, produces explainable recommendations, enforces
risk, and optionally executes and learns from trades. Trading is an optional
application; analysis is the baseline capability.

---

## 2. Functional Requirements

### 2.1 Market Connectivity

- **FR-CONN-01** The platform MUST connect to markets exclusively through
  provider adapters.
- **FR-CONN-02** Business logic MUST NOT depend on any specific provider or
  market type.
- **FR-CONN-03** Adding a new provider MUST require only a new adapter, with no
  change to business logic.
- **FR-CONN-04** Adapters MUST translate provider-specific data into standardized
  domain models before it enters the platform.

### 2.2 Feature Engineering

- **FR-FEAT-01** Every market event MUST be transformed into standardized
  features.
- **FR-FEAT-02** Each feature MUST be computed exactly once.
- **FR-FEAT-03** All subsystems MUST consume features from the Feature Store as
  the single source of truth.
- **FR-FEAT-04** The platform MUST NOT duplicate feature calculations across
  subsystems.

### 2.3 Analysis

- **FR-ANL-01** Analysis MUST be performed by independent engines, each confined
  to a single domain.
- **FR-ANL-02** Analytical engines MUST be pluggable; adding or removing an engine
  MUST NOT modify other engines.
- **FR-ANL-03** There MUST NOT be a single monolithic intelligence core; analysis
  is a federation of engines.

### 2.4 Decision Pipeline

- **FR-DEC-01** The platform MUST progress through distinct stages: Market Event →
  Feature → Insight → Recommendation → Decision → Execution → Outcome → Learning.
- **FR-DEC-02** The platform MUST NOT jump directly from analysis to execution.
- **FR-DEC-03** Each stage MUST be independently observable and testable.

### 2.5 Explainability

- **FR-EXP-01** Every recommendation MUST include: action, confidence, reasons,
  warnings, evidence, and expected time horizon.
- **FR-EXP-02** Every executed action MUST be traceable back to the originating
  market event.

### 2.6 Risk

- **FR-RISK-01** The Risk Engine MUST hold veto authority over every decision.
- **FR-RISK-02** No order MAY reach a venue without passing the Risk Engine.
- **FR-RISK-03** Capital preservation MUST take priority over profitability in all
  risk evaluations.

### 2.7 Execution

- **FR-EXEC-01** Execution MUST be optional and disabled by default.
- **FR-EXEC-02** The platform MUST support paper trading and live trading through
  the same decision pipeline.
- **FR-EXEC-03** Only Risk-approved decisions MAY be routed to execution.

### 2.8 Artificial Intelligence

- **FR-AI-01** AI MAY analyze, explain, summarize, estimate probability, and
  generate insights.
- **FR-AI-02** AI MUST NOT execute trades, bypass the Risk Engine, or bypass
  business rules.
- **FR-AI-03** AI providers MUST be replaceable through the AI Gateway.
- **FR-AI-04** The platform MUST remain coherent and functional with AI disabled.

### 2.9 Learning and History

- **FR-LRN-01** The platform MUST persist features, insights, recommendations,
  decisions, executions, outcomes, and performance.
- **FR-LRN-02** Historical data MUST be queryable and reusable for future
  analysis.

### 2.10 Interfaces

- **FR-IFC-01** The platform MUST expose its intelligence through replaceable
  interfaces (e.g., Telegram, web dashboard, REST, WebSocket).
- **FR-IFC-02** Replacing an interface MUST NOT change business logic.

---

## 3. Non-Functional Requirements

Non-functional requirements have moved to their single home,
`08_NON_FUNCTIONAL_REQUIREMENTS.md`. The identifiers (`NFR-ARCH-*`, `NFR-MOD-*`,
`NFR-OBS-*`, `NFR-CFG-*`, `NFR-TEST-*`, `NFR-SEC-*`) are preserved there, so
existing cross-references remain valid.

---

## 4. Out of Scope

- The platform is not a single-exchange trading bot.
- The platform does not grant AI ownership over execution or risk.
- The platform does not hardcode market- or provider-specific behavior into the
  core.

---

## Related Documents

- `00_VISION.md` — what the product is and why it exists
- `01_PRODUCT_PHILOSOPHY.md` — the principles behind these requirements
- `03_ROADMAP.md` — the order in which requirements are delivered
- `10_ARCHITECTURE.md` — how requirements map to structure
- `32_RISK_ENGINE.md` — risk requirements in detail
