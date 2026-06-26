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
