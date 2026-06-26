# Glossary

> **Question this document answers:** What do our terms mean?

This glossary defines the canonical vocabulary of the platform. Every other
document MUST use these terms with these meanings. Where a concept appears in the
decision pipeline, its position is noted to keep the vocabulary internally
consistent.

---

## Platform and Architecture

**Platform** — The Universal Market Intelligence Platform: an analytical
operating system for financial markets. Not a trading bot; trading is one
application of it.

**Modular Monolith** — The initial deployment form: a single deployable unit
composed of independently replaceable modules with enforced boundaries.

**Module** — A major component with a single responsibility, a well-defined
interface, and explicit dependencies. Independently testable and replaceable.

**Layered Architecture** — Structuring the system into layers (e.g., domain,
application, infrastructure) with controlled dependency direction.

**Clean Architecture** — Dependency rule in which business logic depends on
abstractions, never on infrastructure or providers.

**Event-Driven Architecture** — Style in which components communicate primarily
by publishing and consuming events.

**Event Bus** — The transport over which domain events are published and
consumed between modules.

**Domain Model** — A standardized, provider-agnostic representation of a market
concept used by all business logic.

---

## Markets and Providers

**Market** — A class of tradable instruments (e.g., cryptocurrency, stocks,
forex, commodities, ETFs, futures, prediction markets, options).

**Provider** — An external venue or data source (e.g., Bybit, Binance, OKX,
Hyperliquid, Interactive Brokers, OANDA, Polymarket, Kalshi).

**Adapter** — The only component allowed to hold provider- and market-specific
knowledge. Translates external data into standardized domain models and routes
orders to a provider.

**Market Independence** — The property that core business logic never knows which
market or instrument it operates on.

---

## Data and Features

**Market Event** — A raw, standardized occurrence in a market (e.g., trade,
order-book update, candle close). The first stage of the decision pipeline.

**Feature** — A standardized, computed signal derived from market events.
Calculated exactly once.

**Feature Engine** — The component that transforms market events into features.

**Feature Store** — The single source of truth for features. All subsystems read
features from here; duplicated calculation is prohibited.

---

## Intelligence

**Analysis Engine** — An independent, pluggable component that analyzes a single
domain (e.g., technical, machine learning, LLM, pattern, news, macro, sentiment,
on-chain, liquidity, correlation).

**Insight** — The explainable output of an analysis engine derived from features.
The stage after Feature in the decision pipeline.

**AI Gateway** — The abstraction through which AI providers are accessed and
replaced. Ensures AI providers are swappable without business-logic change.

**Confidence Engine** — The component that quantifies confidence attached to
insights and recommendations.

**Learning Engine** — The component that turns historical outcomes into reusable
knowledge.

---

## Decision and Risk

**Recommendation** — An explainable proposed action combining insights. MUST
include action, confidence, reasons, warnings, evidence, and expected time
horizon. Follows Insight in the pipeline.

**Decision** — A governed selection of a recommendation for potential action.
Follows Recommendation in the pipeline.

**Decision Pipeline** — The mandatory progression: Market Event → Feature →
Insight → Recommendation → Decision → Execution → Outcome → Learning.

**Risk Engine** — The component with absolute veto authority over every decision.
No order reaches a venue without passing it. Capital preservation outranks
profitability.

**Strategy Engine** — The component that defines how insights and recommendations
are turned into candidate decisions.

**Portfolio Engine** — The component that manages positions and allocation across
instruments.

---

## Execution and Learning

**Execution Layer** — The component that routes Risk-approved decisions to a
provider. Optional and disabled by default.

**Paper Trading** — Running the full pipeline with simulated execution and no real
capital.

**Live Trading** — Optional, configuration-gated execution against real capital,
routed only through the Risk Engine.

**Backtest Engine** — The component that replays history to reproduce decisions
deterministically.

**Outcome** — The realized result of an execution. Follows Execution in the
pipeline.

**Learning** — The final pipeline stage: persisting and exploiting outcomes as
historical knowledge.

---

## Cross-Cutting

**Explainability** — The requirement that no recommendation is a black box; each
carries its reasoning and evidence.

**Configuration** — Externalized behavior that varies by market, provider, or
deployment, expressed as data rather than code.

**Observability** — The property that every pipeline stage can be logged,
monitored, and audited after the fact.

**ADR (Architecture Decision Record)** — A document capturing a significant
architectural decision, its context, and its consequences.

---

## Related Documents

- `00_VISION.md` — what the product is and why it exists
- `01_PRODUCT_PHILOSOPHY.md` — principles that use this vocabulary
- `14_DECISION_PIPELINE.md` — the pipeline these terms describe
- `02_REQUIREMENTS.md` — requirements expressed in these terms
