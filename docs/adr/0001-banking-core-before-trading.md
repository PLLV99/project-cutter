# ADR-0001: Build the banking core before the trading features

## Status
Accepted (decided 2026-06-28, confirmed 2026-07-05)

## Context
The original Learning_Plan (June 2026) went straight from database setup to trading orders + Kafka, with no banking phase. The vision document describes a full trading platform. But a trading system needs a working money system underneath: you cannot buy a stock if the app cannot hold, deposit, and reserve cash. The user's first endpoint draft mixed banking and brokerage concepts, which made the design fuzzy.

## Decision
Build in this order: banking core (accounts, deposits, withdrawals, transfers, immutable ledger) → auth → trading orders on top → Kafka/event pipeline → AI risk worker → Docker/CI → frontend. Nothing from the vision is cut — it is re-sequenced. The merged plan lives in `reference/roadmap.html`; the original Learning_Plan is kept frozen in `docs/planning/` as a founding document.

## Consequences
- Easier: each phase has a working foundation under it; the ledger concepts learned in Phase 1 are exactly what orders/settlement need later; better interview story ("I built the money core first, then extended it").
- Harder: the exciting parts (Kafka, AI) come later, which costs motivation — mitigated by keeping them visible on the roadmap.
