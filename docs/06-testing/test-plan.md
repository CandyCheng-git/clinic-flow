# Test Plan and Quality Standards

Version: 0.7 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Test approach

Prioritize transaction integrity and access control before broad UI coverage. Tests must exercise observable business behavior, particularly failures, retries and concurrency. Do not count generated test files or code coverage alone as evidence of correctness.

| Level | Purpose | Proposed tool |
| --- | --- | --- |
| Unit | Quantity, eligibility and state-transition rules | xUnit |
| Integration | API, constraints, transaction boundaries and real PostgreSQL behavior | Testcontainers / HttpClient |
| API contract | Payloads, status codes, errors and pagination | Automated API checks / Postman |
| E2E | Staff purchase/redemption/correction journey | Playwright |
| Regression | Previously verified critical behaviors | Relevant automated suite |
| Concurrency | Distinct requests contend for the same state | Integration tests with coordinated starts |
| Performance | Defined read workload and error rate | k6 |
| Security | Identity failures, permissions, input handling and dependencies | Targeted tests; optional OWASP ZAP |
| UAT | Synthetic-data workflow fit | Authorized participant walkthrough |

## Fixtures

Use Bogus plus custom rules with a fixed random seed, fixture/reference date and generator version. Include zero/one/many-session vouchers; eligible/ineligible services; matching/mismatched customers; active/archived customers; active/disabled staff; all appointment states; missing/stale/confirmed paper references; Draft/Pending/ReviewRequired and lost-response paths; Open/Reversed tickets for both roles, pending/posted replacement, multi-step correction chains and old-operation replay; and DST transition cases. Keep valid seeded fixtures separate from invalid request inputs.

The small and portfolio-scale targets are in the [data dictionary](../03-system-design/data-dictionary.md). Counts are planned. Validate FK relationships, ledger totals, booking intervals and snapshot consistency after generation.

## Meaningful concurrency and failure checks

- Run against real PostgreSQL with independent connections/transactions and an explicit synchronization point.
- For last-session tests, use distinct bookings and keys to isolate balance competition.
- For duplicate-key tests, use the same operation/key/payload simultaneously and assert one committed effect.
- For business-duplicate tests, use different keys on the same booking.
- Inject a failure after the balance mutation but before ledger/audit completion; assert full rollback from a separate connection.
- Force failure after saved completion/signature but before posting; assert retained evidence, visible Pending/ReviewRequired and no partial deduction.
- Race ticket reversals by both roles; assert one original reversal, atomic ticket state and preserved mistake.
- Race replacement vouchers for one reversed deduction and replay historical intents after replacement; inspect at most one active deduction, original-voucher restoration, corrected-voucher debit and chain/receipt integrity (TC-022, TC-037–039).
- Avoid tests whose only evidence is that requests were launched together; inspect final persisted state.

## Development quality standards

Use meaningful names, small focused methods, clear module boundaries, consistent formatting, static analysis, appropriate exception handling and versioned migrations. Avoid hardcoded, unrecorded business assumptions. Review AI-generated critical code and be able to explain its failure modes.

## Definition of Done

A feature is done when its acceptance criteria are met, code builds, relevant unit/integration tests pass, authorization and error paths are checked, documentation matches behavior, review/self-review is recorded and no unresolved critical defect blocks it. Complete relevant E2E checks when the slice reaches the UI.

## Release conditions and evidence

All critical scenarios in the selected release scope must pass with retained results before its quality gate. TC-040–045 are conditional public-mode checks; deferring that mode does not waive any core real-API/PostgreSQL criteria. Record the commit, environment, commands, date, fixture version, test outcomes and known defects in [test-report.md](test-report.md). A security scan alone does not establish production readiness. UAT requires actual participation; no agreement is recorded.

## Optional public-mode verification

Run shared UI/DTO/scenario checks separately against mock and real mode, recording actual mode/build. TC-040–045 cover simulation labels, explicit mode, real-network denial, independent profile storage, coordinated tabs, reset generations and artifact/service-worker separation. Test denied/quota/version storage and blocked/missing worker/handlers. A public-only passing run proves neither real OIDC/RBAC nor PostgreSQL transaction/concurrency behavior. Keep real benchmark/security/integration reports separate; no MSW response timing in API p95 results. Review actual static-host settings/smoke before recording a public URL.

Core database/API review adds TC-046–050 for FK/type/null-shape violations, persisted signing versions, evidence-versus-posting receipt semantics, explicit operation-ID replay, catalog/archive races, insert ordering and pointer/ledger reconciliation. Run migration-level constraints and application-role protections against real PostgreSQL, plus use-case tests for aggregate rules that ordinary FK/CHECK cannot prove. Inspect final persisted state from an independent connection; document review remains separate from executed verification.
