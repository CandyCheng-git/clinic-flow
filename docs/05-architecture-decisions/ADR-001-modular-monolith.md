# ADR-001 — Modular Monolith

Version: 0.1 | Updated: 27 November 2026

Status: Proposed architecture decision

Decision status: **Proposed**. Decision owner: Candy Cheng. Review trigger: first working vertical slice or a demonstrated boundary problem.

## Context

One engineer is building a single-clinic portfolio application. Purchase, balance, ledger and audit changes require dependable transactional consistency. Additional distributed infrastructure would consume time without a validated scaling or team need.

## Options

| Option | Advantages | Costs |
| --- | --- | --- |
| Simple layered monolith | Few deployment units; quick start | Boundaries may blur without discipline |
| Modular monolith | One deployment/database with explicit business boundaries | Requires deliberate ownership and sensible dependency rules |
| Microservices | Independent deployment and scaling where justified | Distributed transactions, networking, service operations and testing overhead |

## Proposed decision

Use one API and database with clear Customers, Appointments, Vouchers, Identity, Reporting and supporting module boundaries. Apply simple presentation/application/domain/infrastructure responsibilities where useful. Do not require a separate project, repository, message bus or generic abstraction for each module.

## Consequences

Core state changes can share one database transaction. Local operation and debugging remain manageable. Independent module deployment is unavailable, which is acceptable for the current scope. Review coupling in code review and tests rather than adding abstractions preemptively.

## Evidence and acceptance

No comparative benchmark or implementation evidence exists. Accept or revise this decision after a runnable purchase/redemption slice demonstrates manageable dependencies and transaction behavior. A design preference is not measured proof of maintainability.
