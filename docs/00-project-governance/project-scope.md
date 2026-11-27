# MVP Scope and Delivery Checklist

Version: 0.7 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Scope boundary

The proposed MVP serves one clinic, with Manager and Receptionist accounts. It uses synthetic customers and transaction data. It is a portfolio demonstration, with no production service commitment.

| In scope | Out of scope for MVP |
| --- | --- |
| Customer profiles, search, history and archiving | Medical records and treatment photographs |
| Basic staff and service management | Payroll and accounting integration |
| Staff appointments and overlap prevention | Multi-branch and customer self-service |
| Package definitions and voucher purchases | Online payments and payment gateways |
| Paper confirmation, automatic deduction, ticket reversal and correct-voucher replacement by either role | Refund, transfer and extension workflows |
| Authentication, RBAC and audit trail | SMS/email notifications and mobile apps |
| Basic operational dashboard and CSV export | Product inventory and AI recommendations |
| Automated integrity tests and local/demo delivery | Microservices and offline synchronization |

Data import is a Could requirement, deferred until core delivery. Room/equipment scheduling is pending validation and outside the current staff-only scheduling assumption. General balance adjustments are deferred; correction means ticket-linked reversal by Owner or Reception, without extra approval. Paper confirmation and draft/review recovery are included; signature-image capture is excluded.

## Conditional presentation extension

DEC-20261127-07 records hybrid as the preferred optional presentation route after real core delivery: shared React UI, MSW simulated API/roles, IndexedDB synthetic persistence and coherent Reset Demo. Cloudflare Pages is a static-host candidate; no public backend/database or Azure/AWS subscription is required. Public mode is labelled simulated and supplies no real backend security/concurrency evidence. FR-D01–D06 and TC-040–045 become requirements only if public mode is selected; they do not add to or complete the 44 core tasks. See [roadmap](development-roadmap.md) and [strategy](../07-deployment/demo-strategy.md).

## First vertical slice

Create a synthetic customer, record purchase, create/complete booking, confirm synthetic customer paper signature, automatically deduct one eligible session and inspect history. Include recovery from failed/uncertain posting and a ticket-linked reversal plus correct-voucher replacement by either role. Prove distinct bookings cannot both consume the last session and retries have no extra effect. Follow with full authenticated UI and remaining MVP features.

## Master checklist

These 44 delivery tasks require review or implementation evidence. Three document-review tasks are complete: R04 (HLD), R05 (ERD/data dictionary) and R06 (transaction/concurrency/idempotency design), recorded on 27 November 2026; the remaining 41 are unchecked. This 3/44 count represents design review evidence, not implemented or database-verified behavior. R09 remains pending because only first-slice API mapping was reviewed; full DTO/OpenAPI/report contracts are incomplete.

### Phase 0 — Foundation: 0/6 verified complete

- [ ] F01 Review project proposal and objectives.
- [ ] F02 Freeze the first MVP scope.
- [ ] F03 Identify stakeholders and decision owners.
- [ ] F04 Validate the described current workflow.
- [ ] F05 Record and review business rules and demo assumptions.
- [ ] F06 Review risks and mitigation owners.

### Phase 1 — Requirements and design: 3/9 document reviews complete

- [ ] R01 Review BRD and proposed requirements.
- [ ] R02 Baseline functional and non-functional requirements.
- [ ] R03 Review stories and acceptance criteria.
- [x] R04 Review HLD and module boundaries — completed 2026-11-27; [review record](../03-system-design/HLD.md#review-record--27-november-2026), document review only; ADRs/runtime checks remain Proposed/pending.
- [x] R05 Review ERD and data dictionary — completed 2026-11-27; [ERD review](../03-system-design/ERD.md#review-record--27-november-2026), proposed schema only; migration checks pending.
- [x] R06 Confirm transaction, concurrency and idempotency design — design review completed 2026-11-27; [transaction review](../03-system-design/LLD.md#transaction-model-review--27-november-2026); real PostgreSQL failure/race verification pending.
- [ ] R07 Review authentication and permission design.
- [ ] R08 Review wireframes and main user journeys.
- [ ] R09 Review API contracts and traceability.

### Phase 2 — Development: 0/10 verified complete

- [ ] D01 Create runnable backend, frontend and local database.
- [ ] D02 Implement schema migrations and deterministic synthetic seed.
- [ ] D03 Implement login, access checks and disabled-account handling.
- [ ] D04 Implement customer profiles, search and archive.
- [ ] D05 Implement basic staff and service administration.
- [ ] D06 Implement appointment lifecycle and overlap protection.
- [ ] D07 Implement package definition and purchase snapshots.
- [ ] D08 Implement paper-confirmation-triggered atomic deduction, draft/review recovery and duplicate protection.
- [ ] D09 Implement both-role ticket-linked reversal/correct-voucher replacement, audit and reconciliation.
- [ ] D10 Implement integrated UI, dashboard and CSV export.

### Phase 3 — Quality assurance: 0/7 verified complete

- [ ] Q01 Run meaningful domain unit tests.
- [ ] Q02 Run PostgreSQL/API integration tests.
- [ ] Q03 Run complete workflow E2E tests.
- [ ] Q04 Pass concurrency, retry and rollback tests.
- [ ] Q05 Check authentication, resource access and dependencies.
- [ ] Q06 Run reproducible benchmarks and retain raw results.
- [ ] Q07 Complete synthetic-data UAT and critical regression review.

### Phase 4 — Deployment: 0/5 verified complete

- [ ] O01 Verify Docker setup from a clean checkout.
- [ ] O02 Run the CI pipeline and retain results.
- [ ] O03 Rehearse demo delivery, migration and rollback.
- [ ] O04 Demonstrate backup and restore to a clean database.
- [ ] O05 Verify health checks, logs and operating instructions.

### Phase 5 — Portfolio: 0/7 verified complete

- [ ] P01 Update documentation to match the implementation.
- [ ] P02 Prepare evidence-backed technical case studies.
- [ ] P03 Prepare truthful STAR explanations.
- [ ] P04 Record a short synthetic-data demo video.
- [ ] P05 Prepare final screenshots using synthetic data.
- [ ] P06 Publish an authorized repository/release.
- [ ] P07 Record lessons, limitations and next steps.

## Scope change rule

Record a change request with business reason, priority, effort, impacted requirements/tests and target release. Prefer the next release for additions unless required for core correctness, security or data integrity. See the [change log](change-log.md).
