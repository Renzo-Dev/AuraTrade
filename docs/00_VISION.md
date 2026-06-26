# Vision

> **Question this document answers:** What is this product, and why does it exist?

This document defines the long-term vision of the platform. It is subordinate
only to `project_context.md` and takes precedence over every other document in
`docs/`.

---

## Problem Statement

Financial markets produce an overwhelming, continuous stream of heterogeneous
data: order books, trades, candles, funding rates, on-chain flows, macro
releases, news, and sentiment. This data arrives in incompatible formats across
incompatible providers. Most market tools treat each market and each provider as
a special case, hard-wire trading logic to a single exchange, and collapse the
distance between a raw price tick and an irreversible order.

The result is fragile software: analysis that cannot be reused across markets,
trading logic that breaks when the venue changes, and decisions that cannot be
explained or audited after the fact.

---

## Product Definition

This platform is a **Universal Market Intelligence Platform**.

It is **not** a cryptocurrency trading bot. Trading is one optional application
built on top of the platform, not its purpose.

The platform is an **analytical operating system for financial markets**. Its
responsibility is to ingest raw market data from any market and any provider,
transform it into standardized intelligence, estimate probabilities, enforce
risk, and — only when explicitly enabled — execute and learn from trades.

---

## What the Platform Does

The platform owns a single, deterministic pipeline:

```
Data → Features → Analysis → Decision → Risk → Execution → Learning
```

1. **Data.** Connect to any market through provider adapters.
2. **Features.** Transform every market event into standardized features,
   computed exactly once and stored in a single Feature Store.
3. **Analysis.** Run independent, pluggable analytical engines, each confined to
   its own domain.
4. **Decision.** Combine insights into explainable recommendations and decisions.
5. **Risk.** Subject every decision to a Risk Engine with absolute veto authority.
6. **Execution.** Optionally route approved decisions to an execution layer.
7. **Learning.** Persist every stage as historical knowledge for future analysis.

---

## Market and Provider Independence

The core platform is independent of market type and provider. It must never know
whether it is operating on BTC, EURUSD, AAPL, gold, or a prediction market.
Provider-specific behavior lives only in adapters. Business logic always operates
on standardized domain models.

Supported market classes include cryptocurrency, stocks, forex, commodities,
ETFs, futures, prediction markets, and options. Supported providers are connected
through adapters and include venues such as Bybit, Binance, OKX, Hyperliquid,
Interactive Brokers, OANDA, Polymarket, and Kalshi.

---

## Role of AI

AI is one analytical component among many — never the owner of the platform.

AI **can** analyze, explain, summarize, estimate probability, and generate
insights. AI **cannot** execute trades, bypass the Risk Engine, or bypass
business rules. AI providers are replaceable through an AI Gateway.

The platform is built around **data**, not around AI.

---

## Non-Negotiable Principles

These principles constrain every downstream decision:

- **Capital preservation over profitability.** Risk protection has absolute
  priority; the Risk Engine holds veto authority.
- **Single source of truth for features.** Features are computed once and shared;
  duplicated calculation is prohibited.
- **Explainability over black boxes.** Every recommendation carries its action,
  confidence, reasons, warnings, evidence, and expected time horizon.
- **Modularity over monoliths of logic.** Every major component is independently
  replaceable, from the AI provider to the exchange to the database.

---

## Success Criteria

The vision is realized when:

- A new market or provider can be added by writing an adapter alone, with no
  change to business logic.
- A new analytical engine can be added as a plug-in without modifying existing
  engines.
- Every executed action can be traced back through decision, recommendation,
  insight, and feature to the originating market event.
- No order can reach a venue without passing the Risk Engine.

---

## Related Documents

- `project_context.md` — foundational project context (highest authority)
- `01_PRODUCT_PHILOSOPHY.md` — the principles that govern product decisions
- `02_REQUIREMENTS.md` — what the platform must do
- `03_ROADMAP.md` — the order in which the platform is delivered
- `10_ARCHITECTURE.md` — how the platform is structured
