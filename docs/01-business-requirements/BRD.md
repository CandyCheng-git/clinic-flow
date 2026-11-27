# Business Requirements Document

Version: 0.4 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Known context and problem statement

The source describes three operational record types: appointment bookings, voucher purchases and remaining voucher sessions. Candy clarified on 27 November 2026 that paper customer files carry customer IDs, an approximately ten-year-old POS is in use, and handwritten transaction slips are later manually entered into the existing system. This establishes a paper-to-POS re-entry step. See the [current-state facts and open questions](business-process.md). ID matching, exact record locations, deduction timing and reconciliation remain unconfirmed.

Paper and POS records coexist (BP-001), and transaction information is re-entered manually (relevant to BP-002); this does not establish that customer registration itself is repeated. Inconsistent balances (BP-003), duplicate deductions (BP-004), incomplete modification history (BP-005), appointment clashes (BP-006), manual reporting (BP-007) and unclear staff access (BP-008) remain hypotheses, not verified clinic incidents. Re-entry effort and error rates are unmeasured.

## Future state

Owner and Reception locate a customer, schedule a service, record a purchase and complete the booking. When customer paper signature is confirmed by staff and completion exists, automatically attempt one deduction. Failures retain saved evidence for recheck; mistakes require a correction ticket which either role may reverse, preserving the original. Reporting uses defined measures rather than treating voucher sales as service revenue.

## Requirements register

All rows are proposed and pending validation. Priority expresses the source blueprint's intent.

| ID | Requirement | Priority | Intended evidence |
| --- | --- | --- | --- |
| BR-001 | Centralize customer records | Must | Search and profile workflow; review paper/POS customer-reference mapping before pilot |
| BR-002 | Centralize appointment records | Must | Calendar and booking history |
| BR-003 | Record treatment-package purchases | Must | Purchase with historical snapshots |
| BR-004 | Maintain accurate remaining sessions | Must | Balance and ledger reconciliation |
| BR-005 | Prevent repeated deductions and negative balances | Must | Duplicate and concurrent-request tests |
| BR-006 | Preserve traceable transactions and ticket-linked corrections | Must | Original mistake, ticket, reversal, correct-voucher replacement and audit; both roles may execute |
| BR-007 | Control staff permissions | Must | Backend role/resource tests |
| BR-008 | Provide basic operational reporting | Should | Defined summaries against known fixtures |
| BR-009 | Support data export | Should | Authorized CSV with validated totals |
| BR-010 | Support historical data import | Could | Future dry run and reconciliation |
| BR-011 | Support multiple branches | Future | Outside MVP |
| BR-012 | Send automatic booking reminders | Future | Outside MVP |

## Measures and limitations

Collect a baseline and define the observation window before comparing customer lookup time, appointment handling time, discrepancies, repeated entry and staff satisfaction. Synthetic benchmarks establish technical behavior; they do not establish business savings or clinic adoption.

## Dependency on validation

Review the [rules and questions](business-rules.md) and [stakeholder register](stakeholders.md). Demo development may proceed with explicit provisional rules; a commercial pilot depends on agreed workflow, data access, ownership and operating responsibilities.

Latest TO-BE source: Candy, 2026-11-27, DEC-20261127-02–05 in [business rules](business-rules.md). Paper method and both-role reversal are user-specified; POS timing/mapping remain open. Draft/recovery mechanics and unchanged role restrictions remain demo proposals.

Candy confirmed DEC-20261127-06 on 2026-11-27: after ticket reversal, another correct voucher may be used. Keep the booking Completed and link original, reversal, ticket and replacement; at most one active deduction. Replacement failure does not roll back an earlier committed reversal.
