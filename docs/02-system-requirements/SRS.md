# Software Requirements Specification

Version: 0.5 | Updated: 27 November 2026

Status: Review draft — user-clarified requirements; not implemented or application-verified

## Review summary（本次確認）

Candy clarified on 27 November 2026: 老闆及 Reception 可處理日常資料；預約完成、客人簽紙本並由員工在系統確認後，自動扣一次；重試不得多扣、並發不得透支；扣錯須開更正 ticket 並保留原紀錄；老闆及 Reception 均可執行沖銷，毋須額外老闆批准。

These are requested TO-BE requirements, not independent observation of legacy POS behavior or a recorded clinic-wide sign-off. Existing requirement IDs are retained. FR-B08–B10 and FR-V11–V12 add paper confirmation, draft/review recovery and correction tickets. Candy subsequently confirmed that a reversed deduction can be replaced using another correct voucher; FR-V13 and DEC-20261127-06 capture this change. All implementation/test results remain pending.

## Purpose, roles and boundary

Single-clinic, synthetic-data staff MVP. Owner（老闆）maps to existing Manager; Reception maps to Receptionist. Both may manage customers, bookings and purchases, confirm paper signatures, recheck failed deductions, create correction tickets and execute ticket-linked reversals. No extra Owner approval or different-person requirement applies. Catalog, staff administration and sales-report restrictions remain Manager-only demo assumptions pending review. Customers sign paper and have no system login.

See [scope](../00-project-governance/project-scope.md), [rules](../01-business-requirements/business-rules.md), [acceptance criteria](acceptance-criteria.md) and [traceability](traceability-matrix.md). A correction ticket is an internal ClinicFlow record; no Jira integration is added.

## Current-state constraint

Paper customer files have customer IDs; handwritten slips are manually entered into an approximately ten-year-old POS (Candy, 2026-11-27). Paper/POS ID mapping, record authority, reconciliation and legacy deduction timing remain unverified. The ClinicFlow trigger is specified below; it does not establish how the POS currently deducts.

FR-C03/FR-C04 retain separate internal customer IDs. Source-labelled paper/POS references require mapping validation. No POS API, synchronization, OCR, historical import, opening balances or replacement of manual POS entry is assumed.

## Workflow, input and failure behavior

1. Owner or Reception locates the customer, records a package purchase, creates/completes a booking and selects the purchased voucher. Before paper signing, show customer, booking, service, voucher and one-session usage for review. Do not silently choose among multiple qualifying vouchers.
2. The customer signs paper. An active staff user confirms that signature in ClinicFlow with a bounded paper reference. Save confirming staff, server confirmation time and the frozen customer/booking/service/voucher/quantity summary. Optional reported paper-signing time is separate; server confirmation time is not proof of witnessed signing time. Signature image upload, digital handwriting and legal identity verification are outside this MVP.
3. When completion and valid staff-confirmed paper signature both exist, automatically attempt one deduction. There is no extra normal-redemption confirmation button. Neither prerequisite alone deducts; if confirmation exists first, subsequent completion invokes the same use case.
4. A successful deduction atomically commits balance, immutable ledger, audit, operation receipt and Redeemed state. Show server-returned balance/transaction ID only after commit.
5. Completion and signature evidence may persist before the deduction transaction. A confirmed failure leaves no partial deduction; show ReviewRequired and a safe reason while retaining saved evidence for staff recheck. Never describe an unsaved form as persisted.
6. A timeout/lost response shows OutcomeUnknown. Query the persisted operation and retry the same intent/key; do not assume rollback or silently create a new deduction. A crash after evidence save but before posting leaves a visible Pending item recoverable through recheck using the same automatic use case.
7. Save incomplete input as a server-side Draft with ID/revision. Draft save posts no purchase, completion, signature confirmation or deduction. Reopen saved values for correction. Unchanged signed details can retry the same intent; changed customer/service/voucher/quantity needs a fresh reviewed paper confirmation, retaining superseded evidence. Changed payloads must not reuse the previous key.
8. For a committed mistake, either role creates a correction ticket linked to original Redemption, customer, booking and voucher, with reason and creator/time. Open tickets do not alter balances. Either role may execute reversal without extra approval. One +1 entry, balance, audit, receipt and ticket Reversed transition commit together. Retrying returns the saved result; concurrent tickets cannot reverse one original twice. No direct history or balance edits.

Booking lifecycle remains Scheduled/Confirmed/InProgress/Completed/Cancelled/NoShow. Separate deduction progress is AwaitingSignature/Pending/ReviewRequired/Redeemed/Reversed. Draft describes editable workflow input, not a posted booking state. OutcomeUnknown is client uncertainty pending a server query. Rollback of posting cannot erase evidence committed earlier.

**Revised DA-07 / DEC-20261127-06:** after a ticket-linked reversal commits, either role may replace that deduction using another eligible voucher belonging to the same customer. At most one un-reversed deduction may exist for a booking at any time; history can contain multiple reversed deductions. Here cancel means reversal of a deduction, not changing the Completed booking to Cancelled.

Replacement uses a fresh operation and reviewed paper confirmation covering the corrected voucher, linked to the ticket and original deduction; do not silently reuse a signature summary for a different voucher. The existing paper-confirmation method remains; detailed correction/endorsement form wording is a proposed design for review. Once valid corrected confirmation exists, automatically attempt -1 on the correct voucher without an extra normal-redemption approval. The original +1 reversal remains committed if replacement fails: show AwaitingReplacement/ReviewRequired until resolved. Reversal alone never re-runs the old deduction. Old request/key replay returns its recorded historical result, not a new posting. One successful replacement is allowed per reversed deduction/ticket; if that replacement is later wrong, use a new ticket and reversal for that specific new deduction.

## Functional requirements and checkable examples

Both = active Owner/Manager or Receptionist. Common rules: server validation, active-staff authorization, bounded input, safe errors, stale-write rejection and retained saved input for recheck. Examples are planned checks, not passing results.

| Module / ID | Actor | Required behavior and inputs | Checkable success / failure example | Acceptance / test |
| --- | --- | --- | --- | --- |
| Customers / FR-C01 | Both | Create with display name and optional contact | Valid name returns stable ID; blank name creates nothing | AC-C / TC-013 |
| Customers / FR-C02 | Both | Update allow-listed fields with revision | Change retains relationships; stale revision cannot overwrite | AC-C / TC-013 |
| Customers / FR-C03 | Both | Search name/contact with bounded pagination | Page 50 returns at most 50; shared phone never merges people | AC-C / TC-013 |
| Customers / FR-C04 | Both | Open profile by internal ID | Correct profile shown; unknown ID not found | AC-C / TC-013 |
| Customers / FR-C05 | Both | View appointment history | Only selected customer bookings appear in stable order | AC-C / TC-014 |
| Customers / FR-C06 | Both | View purchased vouchers and balances | 5-session purchase shows 5; other customer vouchers excluded | AC-C / TC-014 |
| Customers / FR-C07 | Both | Archive while retaining history | History remains; new booking/purchase rejected | AC-C / TC-014 |
| Appointments / FR-B01 | Both | Create with active customer/staff/service and start/end | Valid slot saved; end before start rejected with form retained | AC-B / TC-015, TC-016 |
| Appointments / FR-B02 | Both | Reschedule Scheduled/Confirmed booking with revision | Valid change saved; stale/overlapping change rejected | AC-B / TC-015 |
| Appointments / FR-B03 | Both | Cancel permitted booking with reason | State persists; no deduction | AC-B / TC-016 |
| Appointments / FR-B04 | Both | Complete; auto-attempt deduction if paper confirmation exists | Completion without confirmation leaves 5; with confirmation leaves 4 | AC-B, AC-S / TC-016, TC-029 |
| Appointments / FR-B05 | Both | Prevent concurrent staff overlap | Overlapping requests commit at most one; adjacent times allowed | AC-B / TC-008, TC-015 |
| Appointments / FR-B06 | Both | View daily/weekly calendar with staff/date filters | Only selected clinic-date bookings shown; empty date has empty state | AC-B / TC-012, TC-015 |
| Appointments / FR-B07 | Both | Record lifecycle and separate deduction progress | Invalid transition rejected; Completed/ReviewRequired visible | AC-B / TC-016, TC-031 |
| Appointments / FR-B08 | Both | Confirm paper signature, reference and frozen usage summary | Actor/time recorded; false/missing confirmation cannot trigger deduction | AC-S / TC-029, TC-030 |
| Appointments / FR-B09 | Both | Save/reopen bounded incomplete workflow Draft | Reload retains values; no purchase/completion/signature/ledger posting | AC-D / TC-031 |
| Appointments / FR-B10 | Both | Recheck pending/failed/unknown operation safely | Lost success response resolves to original; no fresh duplicate deduction | AC-D / TC-031, TC-032 |
| Vouchers / FR-V01 | Manager | Create package with sessions/price/eligible services | Positive whole sessions accepted; zero sessions rejected | AC-P / TC-017 |
| Vouchers / FR-V02 | Manager | Update catalog without rewriting purchases | Price/eligibility edits leave prior snapshots unchanged | AC-P / TC-017 |
| Vouchers / FR-V03 | Both | Record purchase with key and active customer/package | One voucher/opening entry/audit/receipt; retry adds nothing | AC-P / TC-017, TC-018 |
| Vouchers / FR-V04 | Both | View available sessions | 5 minus one deduction shows 4 and reconciles with ledger | AC-V / TC-001, TC-027 |
| Vouchers / FR-V05 | System via Both | Auto-deduct one after completion and paper confirmation | Valid signed completed usage changes 5 to 4 without extra click | AC-S, AC-V / TC-001, TC-029 |
| Vouchers / FR-V06 | System | Protect transport retries and business duplicates | Same key returns historical result; no second active deduction; replacement only after linked reversal | AC-V / TC-003, TC-020, TC-021 |
| Vouchers / FR-V07 | System | Prevent negative balances under concurrent writes | Two signed completed bookings race for final session: one -1 only | AC-V / TC-002, TC-004 |
| Vouchers / FR-V08 | Both | Show ledger, ticket and reversal links | Original mistake remains visible beside correction ticket/+1 | AC-V, AC-T / TC-022, TC-033 |
| Vouchers / FR-V09 | Both | Reverse through valid Open ticket with reason | One original session restored; missing ticket/double reversal rejected | AC-T / TC-009, TC-022, TC-033 |
| Vouchers / FR-V10 | System | Validate signed summary, ownership, service and active access | Wrong customer/service or unsigned/incomplete booking cannot deduct | AC-S, AC-V / TC-010, TC-019, TC-030 |
| Vouchers / FR-V11 | Both | Create correction ticket with original and reason | Open ticket has actor/time, no balance effect; blank reason rejected | AC-T / TC-033 |
| Vouchers / FR-V12 | Both | Inspect ticket, reversal and replacement progress | Failed reversal leaves Open; after reversal, pending replacement remains visible until posted | AC-T / TC-034, TC-038 |
| Vouchers / FR-V13 | Both | After committed reversal select another eligible same-customer voucher; confirm corrected paper summary; automatically post linked replacement | A restored once, B deducts once; missing reversal/wrong voucher rejected; failed B remains pending; retries/races cannot double-post | AC-T / TC-022, TC-037, TC-038, TC-039 |
| Identity / FR-A01 | Both | Login/logout with selected provider | Active login works; logout clears local session/token state | AC-A / TC-035 |
| Identity / FR-A02 | System | Validate API access tokens | Wrong signature/issuer/audience or ID token returns 401 | AC-A / TC-023 |
| Identity / FR-A03 | System | Enforce roles and resource checks | Reception may reverse via ticket; cannot read sales reports | AC-A / TC-007, TC-026, TC-033 |
| Identity / FR-A04 | Manager | Restrict staff/catalog administration and sales data | Reception admin API call returns 403; ticket reversal allowed | AC-A / TC-007 |
| Identity / FR-A05 | System | Handle missing/expired/invalid tokens safely | 401 prompts sign-in; no mutation; server draft retained | AC-A / TC-006, TC-023 |
| Identity / FR-A06 | System | Reject disabled/unmapped staff | Otherwise valid disabled-user token returns 403 | AC-A / TC-024 |
| Reporting / FR-R01 | Both | Show daily states and pending signature/review counts | Counts match fixed fixture including ReviewRequired | AC-R / TC-025 |
| Reporting / FR-R02 | Manager | Show recorded voucher sales by purchase date | Historical-price total matches fixture; not cash receipt/revenue | AC-R / TC-025 |
| Reporting / FR-R03 | Manager | Show current outstanding sessions and as-of time | Current ledger-backed balances match fixture | AC-R / TC-025 |
| Reporting / FR-R04 | Both | Show cancellation/no-show counts | Fixture counts match; no automatic deduction | AC-R / TC-025 |
| Reporting / FR-R05 | Authorized reader | Filter applicable measures by clinic dates | Inclusive start/exclusive end matches boundary fixture | AC-R / TC-012, TC-025 |
| Reporting / FR-R06 | Manager | Export bounded authorized filtered CSV | Rows/totals match; forbidden user denied; formula-like text safe | AC-R / TC-026 |

Basic staff/service administration and audit support these workflows. Other role restrictions remain demo assumptions; latest user answers allow both-role ticket reversal.

## Conditional public-demo requirements — DEC-20261127-07

The supplied hybrid proposal sets the preferred optional presentation direction after real core delivery; local/video remains a fallback. Six additional requirements apply if public mode is selected. They do not change existing real-mode FR-C/B/V/A/R or their evidence requirements. See [strategy](../07-deployment/demo-strategy.md).

| Module / ID | Actor | Required behavior and inputs | Checkable success / failure example | Acceptance / test |
| --- | --- | --- | --- | --- |
| Demo / FR-D01 | Builder | Build shared UI in explicit mock/live modes | Unknown mode fails; visitor cannot enable live access in public build; live has no mock interception | AC-DM / TC-040, TC-045 |
| Demo / FR-D02 | Visitor | See persistent simulated-backend label and simulated Owner/Reception selector | All screens labelled; role selection grants no real credentials/API access | AC-DM / TC-040 |
| Demo / FR-D03 | Visitor | Try synthetic customer/purchase/booking/paper-confirmation/deduction/ticket-replacement/history/dashboard flows | Scenario outcomes match intended UI behavior; no PostgreSQL/OIDC proof claimed | AC-DM / TC-042 |
| Demo / FR-D04 | Visitor | Persist versioned synthetic state per browser profile/origin | Reload retains own data; independent profiles isolated; denied/full storage errors have no API fallback | AC-DM / TC-043 |
| Demo / FR-D05 | Visitor | Confirm Reset Demo and restore coherent seed | All related records reset together; stale requests/tabs cannot repopulate old state | AC-DM / TC-044 |
| Demo / FR-D06 | Builder | Prepare static-only public artifact without real business fallback | Missing worker/handler fails explicitly; no localhost/cloud API/OIDC request or real secret; hosting review before release | AC-DM / TC-041, TC-045 |

## Non-functional requirements and measurement conditions

Numerical limits are proposed engineering/demo targets, not measured clinic requirements. None has been executed.

| ID | Attribute | Proposed requirement / verification |
| --- | --- | --- |
| NFR-001 | Security | Every protected endpoint checks active staff and role/resource permission; planned 401/403 cases deny mutation/disclosure; test both-role ticket success separately |
| NFR-002 | Performance | For real API mode, per read endpoint warm p95 < 500 ms, zero unexpected errors, >=1,000 measured requests under workload below; TC-028 |
| NFR-003 | Reliability | Inject purchase/deduction/reversal failure after intermediate writes: no partial balance/ledger/audit/receipt/ticket transition; saved completion/signature remains; TC-005, TC-031, TC-034 |
| NFR-004 | Integrity | Real PostgreSQL duplicate-key, booking, last-session and double-reversal races yield one permitted effect and ledger = balance; TC-003, TC-004, TC-009, TC-021, TC-027, TC-037, TC-039 |
| NFR-005 | Maintainability | Review critical use-case boundaries, transaction protocols and error paths; retain review notes before implementation baseline gate |
| NFR-006 | Bounded access | Page default 50/max 100; reject invalid sizes; report/export at most 31 clinic dates and export max 10,000 rows; reject oversize without silent truncation; TC-036 |
| NFR-007 | Usability | Walk through purchase, paper confirmation, failure recovery and correction as each role; record participant/help/blockers; no false success or loss of saved drafts |
| NFR-008 | Accessibility | Core UI controls support keyboard, visible focus, labels and text status/errors; keyboard walkthrough and automated accessible-name/contrast checks before UI release |
| NFR-009 | Observability | Mutation attempt has correlation ID and safe outcome; committed change links actor/booking/voucher/ticket; inspect success/failure/unknown-response paths without tokens or signature images |
| NFR-010 | Recovery | Restore to empty DB; verify counts/FKs/ledger, confirmations/drafts/tickets, then run one workflow; TC-011; production RPO/RTO pending |
| NFR-011 | Testability | Test rules and real isolated PostgreSQL failure/concurrency behavior; retain fixture/commit/commands/results; mocks alone cannot prove transaction guarantees |
| NFR-012 | Portability | Clean local setup with pinned dependencies and Docker Compose can seed synthetic data and run completion/signature/ticket workflow; record environment/failures |
| NFR-013 | Privacy | Synthetic-only artifacts/references, no signature image capture; live paper references require active staff. Public mode has simulated roles/confirmations and browser-local synthetic data only. Inspect logs/exports/screenshots for secrets, sensitive bodies and real records |
| NFR-014 | Mode/evidence separation | Conditional public release: no real API/OIDC fallback or real secrets, coherent isolated/resettable browser state, persistent simulation labels. Real security/integrity/performance gates use live API/PostgreSQL evidence (TC-040–045) |

Performance workload: release build on recorded local CPU/RAM/OS, pinned API/PostgreSQL/container versions, local network path and no competing benchmark. Fixed-seed fixture: 1,000 customers, 8 staff, 10,000 bookings, 1,500 purchases, 5,000 ledger entries; verify actual counts first. Run customer search (page 50), daily staff calendar, customer voucher history (page 50) and Manager summary (7 clinic dates) separately. Use 5 concurrent authenticated virtual users, no think time, 60-second warm-up excluded, then 5 measured minutes per endpoint; extend until >=1,000 requests. Measure request-start to full-response elapsed time on local client path. Retain exact inputs, raw samples, throughput, cache conditions and every error/timeout. Unexpected errors fail the run; do not discard failed samples to improve p95. Report cold starts separately. No hosted SLA implied. See [performance plan](../06-testing/performance-report.md).

## Remaining decisions and review gate

| Decision | Position |
| --- | --- |
| ClinicFlow trigger | User specified: completed booking + customer paper signature confirmed by staff automatically attempts deduction |
| Operating roles / reversal | User specified: Owner and Reception can operate, create tickets and reverse; no extra approval |
| Paper confirmation | Paper method selected; bounded reference and frozen summary are proposed design. Form wording, reference format, custody and retention pending |
| Legacy records | Paper/POS ID mapping, record authority, actual POS timing and reconciliation remain pending |
| Recovery | Draft/ReviewRequired/OutcomeUnknown behavior is proposed implementation detail for review |
| After reversal | User confirmed: can use another correct voucher; DA-07 now allows correction chain with at most one active deduction; paper correction form wording remains proposed |
| Other policies | Expiry, penalties, refunds, transfers and fractional usage remain pending; explicit demo assumptions apply |
| Other permissions | Catalog/staff/report restrictions remain demo assumptions |

Step 05 is a **Review draft** with functional examples and measurable NFR proposals. Requirement review, open-decision disposition and traceability review are still needed to baseline it. Documentation is not evidence of application test execution.

## Proposed toolchain

| Layer / activity | Candidate |
| --- | --- |
| Frontend | React, TypeScript, Vite, Tailwind CSS |
| Backend / ORM | C# / ASP.NET Core 10 Web API; EF Core with compatible PostgreSQL provider |
| Database | PostgreSQL; pin a supported version during setup |
| API contract | REST; OpenAPI; interactive documentation where configured |
| Identity | Real mode OAuth 2.0/OIDC + PKCE; Auth0 candidate; self-hosted option may be evaluated if fully local identity needed. Public role selector is simulation only |
| Access policies | Backend RBAC and resource checks |
| Unit tests | xUnit; Moq only where a meaningful substitute is needed |
| Integration tests | Testcontainers and real PostgreSQL |
| E2E / API checks | Playwright / Postman |
| Performance / security | k6 / OWASP ZAP; targeted checks after core implementation |
| Synthetic data | Bogus with a fixed seed and custom invariant checks |
| Packaging / CI | Docker, Docker Compose, Git, GitHub Actions |
| Project management | GitHub Issues / Projects; Markdown and Mermaid |
| Optional public mode | MSW browser API simulation; IndexedDB business data; LocalStorage only for non-sensitive preferences |
| Preferred static-host candidate | Cloudflare Pages static files, after plan/settings review; no edge Functions/database required |
| Future full-stack hosting | Render/Neon optional and unverified; Azure Cloud/AWS deferred; Azure DevOps optional |

Candidate services are not confirmed free-plan recommendations. Before setup, verify terms, quotas, idle behavior, region, retention, billing controls and required features. Use local demo/video if they do not fit the zero-spending target.

## Mode boundary

Proposed VITE_API_MODE=mock or live is build configuration, not a working setup or security boundary. Use separate public/live artifacts and origins. Public mock readiness precedes app API calls; failure stops simulation instead of reaching real services. Same-origin tabs share browser state; independent profiles are isolated. Mock results never satisfy real token/transaction/concurrency/benchmark gates. See strategy for persistence/reset/network checks.

## Data and time standards

Use decimal/numeric money in AUD; preserve historical purchase price and entitlement. Store appointment instants consistently in UTC-capable database fields and interpret clinic dates in `Australia/Melbourne`. Handle ambiguous and nonexistent local times explicitly. Persist explicit statuses rather than inferring them from null fields.
