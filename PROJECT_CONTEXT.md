# PROJECT_CONTEXT.md

# Market Intelligence Platform

## Purpose

This document defines the fundamental vision of the project.

It should be read before writing any documentation, code or architecture.

This document has higher priority than any implementation details.

---

# Project Overview

The project is **NOT** a cryptocurrency trading bot.

The project is a **Universal Market Intelligence Platform**.

Its purpose is to analyze financial markets, transform raw market data into structured intelligence, estimate probabilities, manage risk and optionally execute trades.

Trading is only one application of the platform.

The platform itself is an analytical operating system for financial markets.

---

# Supported Markets

The architecture must be designed from the beginning to support multiple markets.

Examples:

* Cryptocurrency
* Stocks
* Forex
* Commodities
* ETFs
* Futures
* Prediction Markets
* Options

The core platform must remain independent of market type.

---

# Supported Providers

Markets are connected through adapters.

Examples:

* Bybit
* Binance
* OKX
* Hyperliquid
* Interactive Brokers
* OANDA
* Polymarket
* Kalshi

Business logic must never depend on any specific provider.

---

# Core Architecture

The architecture is based on several principles.

1. Layered Architecture

2. Clean Architecture

3. Event Driven Architecture

4. Modular Monolith

The project should initially be implemented as a Modular Monolith.

Every major component must be independently replaceable.

Future migration to microservices should require minimal changes.

---

# Core Philosophy

The platform is built around data.

Not around AI.

AI is only one analytical component.

Data → Features → Analysis → Decision → Risk → Execution → Learning

Everything follows this pipeline.

---

# High-Level Flow

Market Provider

↓

Market Adapter

↓

Market Data

↓

Feature Engine

↓

Feature Store

↓

Analysis Engines

↓

Decision Engine

↓

Risk Engine

↓

Execution Layer

↓

Learning Engine

↓

Statistics

↓

Interfaces

---

# Analysis Engines

There is NO single Intelligence Core.

Instead, the platform consists of independent analytical engines.

Examples:

Technical Analysis Engine

Machine Learning Engine

LLM Engine

Pattern Recognition Engine

News Engine

Macro Engine

Sentiment Engine

On-chain Engine

Liquidity Engine

Correlation Engine

Future analytical engines should be pluggable.

Each engine analyzes only its own domain.

---

# Decision Model

The platform never jumps directly from analysis to execution.

Reasoning pipeline:

Market Event

↓

Feature

↓

Insight

↓

Recommendation

↓

Decision

↓

Execution

↓

Outcome

↓

Learning

Every stage represents a separate business concept.

---

# AI Philosophy

AI is never the owner of the platform.

AI cannot:

* execute trades
* bypass risk
* bypass business rules

AI can:

* analyze
* explain
* summarize
* estimate probability
* generate insights

AI providers must be replaceable through AI Gateway.

---

# Risk Philosophy

Risk protection has absolute priority.

The Risk Engine has veto authority.

No trade may bypass Risk Engine.

Capital preservation is always more important than profitability.

---

# Feature Philosophy

Every market event is transformed into standardized features.

Features are calculated exactly once.

Every subsystem consumes identical features.

No duplicated calculations are allowed.

Feature Store is the single source of truth.

---

# Market Independence

The platform must never know whether it works with:

BTC

EURUSD

AAPL

Gold

Prediction Markets

Only adapters know provider-specific details.

Business logic always works with standardized domain models.

---

# Modularity

Every major component should be replaceable.

Examples:

Replace AI Provider

↓

No business logic changes.

Replace Exchange

↓

No strategy changes.

Replace Database

↓

No domain changes.

Replace Telegram

↓

No business logic changes.

---

# Explainability

Every recommendation must include:

Action

Confidence

Reasons

Warnings

Evidence

Expected Time Horizon

Nothing should be a black box.

---

# Learning Philosophy

Every recommendation becomes historical knowledge.

The platform continuously stores:

Features

Insights

Recommendations

Decisions

Execution

Outcome

Performance

This historical dataset becomes one of the platform's most valuable assets.

---

# Engineering Principles

The project follows:

SOLID

Clean Architecture

DDD concepts where appropriate

Dependency Injection

Interface-driven design

Configuration over hardcoding

Deterministic business logic

Observable systems

Testable components

High cohesion

Low coupling

---

# Documentation Requirements

Every Markdown document should answer one question only.

Avoid mixing architecture, requirements and implementation.

Documents should remain small, focused and easy to maintain.

Cross-reference related documents when appropriate.

---

# AI Documentation Task

Generate professional software architecture documentation.

Write as if preparing documentation for a commercial enterprise platform.

Avoid toy examples.

Avoid simplified explanations.

Prefer engineering terminology.

Use consistent vocabulary across every document.

Maintain the same architectural principles throughout the documentation.

Do not invent conflicting concepts.

Every document should be internally consistent with the entire project.
