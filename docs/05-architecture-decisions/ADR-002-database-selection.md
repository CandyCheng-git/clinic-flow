# ADR-002 — Database Selection

Version: 0.1 | Updated: 27 November 2026

Status: Proposed architecture decision

Decision status: **Proposed**. Decision owner: Candy Cheng. Review trigger: local setup, provider compatibility or integrity-test results.

## Context

The system needs relational references, constraints, transactions, concurrency protection and reproducible local integration tests. The project owner also has SQL Server experience. No production provider has been selected.

## Options

| Option | Fit | Trade-offs to review |
| --- | --- | --- |
| PostgreSQL | Relational integrity, row locking and local container workflow; range/exclusion option for scheduling | Provider/version compatibility and deployment setup |
| SQL Server | Familiar ecosystem and suitable relational transaction capabilities | Edition/license/deployment choices must fit the intended environment |
| SQLite | Convenient small local setup | Different concurrency behavior from the proposed deployment; not the baseline integrity-test database |

## Proposed decision

Use PostgreSQL with EF Core and a compatible provider. Pin versions during implementation and run critical tests against the same database family used for the demo. Do not infer PostgreSQL behavior from an in-memory ORM substitute.

## Consequences

Voucher operations can use explicit database transactions and row locks. Scheduling may use a staff-row locking protocol, with range exclusion constraints as a reviewed alternative. This design introduces database-specific behavior that must be documented and tested. It does not establish that PostgreSQL is universally faster or cheaper.

## Evidence and acceptance

Validate migrations, FK/unique/check constraints, last-session race behavior, simultaneous key claims and backup recovery. No such results exist yet. A hosted PostgreSQL service remains optional and subject to current terms and required features.

References: [PostgreSQL locking](https://www.postgresql.org/docs/18/explicit-locking.html) and [range types](https://www.postgresql.org/docs/18/rangetypes.html). Reference pages are mechanism documentation, not project test evidence.
