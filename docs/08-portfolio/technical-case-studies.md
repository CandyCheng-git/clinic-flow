# Technical Case Study Plans

Version: 0.4 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

These are case-study outlines, not completed project achievements. Fill results only from retained implementation and test evidence.

## Case 1 — Protecting the last voucher session

Problem: two staff requests may try to consume one remaining session. Proposed approach: row-lock the voucher, validate after locking, atomically persist balance/ledger/audit and verify with separate PostgreSQL connections. Demonstrate both success and conflict, and inspect final state.

Evidence required: implementation commit, transaction boundary, TC-004/TC-005 outputs, dataset and actual persisted ledger. Discuss why a UI-disabled button or a read-then-write without concurrency protection would be insufficient.

## Case 2 — Handling retries and business duplicates

Problem: a user can repeat an operation after a lost response, or submit the same booking using a new key. Proposed approach: atomic idempotency records for transport retries plus booking row locking/active-deduction pointer and unique operation/predecessor links for business duplicates.

Evidence required: same-key simultaneous requests, changed-payload conflict, new-key same-booking attempt, response persistence and transaction counts. Explain why idempotency alone does not define every business invariant.

## Case 3 — Correcting without rewriting history

Problem: editing a previous deduction hides the original event. Proposed approach: both-role ticket-linked reversal with a reason, one-reversal constraint, atomic restoration and correct-voucher replacement retaining history.

Evidence required: permission denial, first and second reversal behavior, original/reversal entries and reconciliation. Demonstrate authorized replacement after reversal, at most one active booking deduction, old-operation replay protection and the original/reversal/ticket/replacement chain (TC-022, TC-037–039).

## Case 4 — Scheduling and identity boundaries

Problem: a frontend check cannot prevent simultaneous appointment conflicts, and a valid token does not prove permission to every operation. Proposed approach: transactional staff scheduling lock, explicit lifecycle and backend token/active-staff/role/resource checks.

Evidence required: overlapping writes/reschedule tests, DST cases, invalid token cases, disabled staff and wrong-customer voucher attempts.

## Case 5 — Measuring an optimization

Start with a repeatable query workload and correct results. Capture a baseline, inspect query plans, implement one justified change and rerun under the same conditions. Record trade-offs and raw results. No improvement percentage is available yet.

## Evidence record template

For each case, retain context, requirement/rule IDs, options, chosen design, implementation commit, test commands/results, safe screenshots, measured limitations and a short explanation of what you personally implemented. A synthetic project result must remain distinct from a past employer's achievement.
