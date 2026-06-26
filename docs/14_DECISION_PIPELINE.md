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
