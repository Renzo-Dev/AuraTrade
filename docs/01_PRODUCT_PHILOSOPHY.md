# Product Philosophy

> **Question this document answers:** What principles govern product decisions on
> this platform?

This document states the durable beliefs that shape *what* we build and *what we
refuse to build*. When two valid options conflict, these principles decide. They
are stable; features are not.

---

## 1. Data is the Center of Gravity

The platform is built around data, not around AI and not around any single
strategy. Every capability is a transformation of data along the pipeline:

```
Data → Features → Analysis → Decision → Risk → Execution → Learning
```

A feature, an engine, or an interface earns its place only by adding value to
this flow. Anything that bypasses the pipeline is rejected by design.

---

## 2. The Platform is Market-Agnostic

The core never knows which market or instrument it serves. The same code path
must work for a crypto perpetual, an equity, a forex pair, a commodity, or a
prediction-market contract.

Consequence: market- and provider-specific knowledge is quarantined inside
adapters. Business logic operates exclusively on standardized domain models.

---

## 3. Risk Has Absolute Priority

Capital preservation outranks profitability in every decision. The Risk Engine
holds veto authority and no decision — human, statistical, or AI-generated — may
bypass it.

Consequence: profitability features are never allowed to weaken risk controls.
When in doubt, the platform does less, not more.

---

## 4. Nothing is a Black Box

Every recommendation must be explainable to a human. It carries its action,
confidence, reasons, warnings, evidence, and expected time horizon.

Consequence: an analytical technique that cannot expose its reasoning is treated
as an input to be explained, never as a final authority.

---

## 5. Features are Computed Once

The Feature Store is the single source of truth. A given feature is calculated
exactly once and consumed identically by every subsystem.

Consequence: duplicated or subsystem-local feature math is a defect. If two
engines need the same signal, they read the same stored feature.

---

## 6. Every Major Component is Replaceable

Replacing the AI provider, the exchange, the database, or the notification
channel must not force changes to business logic.

Consequence: components communicate through well-defined interfaces and events.
We accept the cost of abstraction at boundaries to protect the core from churn.

---

## 7. Decisions are Earned, Not Assumed

The platform never jumps from analysis to execution. It moves through distinct
business concepts:

```
Market Event → Feature → Insight → Recommendation → Decision → Execution → Outcome → Learning
```

Consequence: each stage is a separate, observable, testable concept. Collapsing
stages for convenience is prohibited.

---

## 8. AI Serves, It Does Not Govern

AI analyzes, explains, summarizes, estimates probability, and generates insights.
It cannot execute, cannot bypass risk, and cannot bypass business rules. AI
providers are replaceable through the AI Gateway.

Consequence: removing AI entirely must leave a coherent, functioning platform.

---

## 9. Learning is a First-Class Output

Every recommendation becomes historical knowledge. The platform continuously
persists features, insights, recommendations, decisions, executions, outcomes,
and performance.

Consequence: the historical dataset is treated as a primary asset, not as logs.
Data models are designed so that history is queryable and reusable.

---

## 10. Configuration Over Hardcoding

Behavior that may vary across markets, providers, or deployments is expressed as
configuration, not as code branches.

Consequence: thresholds, limits, enabled engines, and provider settings live in
configuration and are observable at runtime.

---

## What We Deliberately Do Not Do

- We do not couple business logic to any provider or market.
- We do not let AI own execution or override risk.
- We do not duplicate feature calculations across subsystems.
- We do not ship recommendations that cannot be explained.
- We do not optimize profitability at the expense of capital preservation.

---

## Related Documents

- `00_VISION.md` — what the product is and why it exists
- `02_REQUIREMENTS.md` — what the platform must do
- `17_DEPENDENCY_RULES.md` — how these principles are enforced in code structure
- `32_RISK_ENGINE.md` — the component that enforces risk priority
- `60_ENGINEERING_PRINCIPLES.md` — engineering practices derived from this philosophy
