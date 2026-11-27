# Test Cases

Version: 0.7 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

All cases are **Not run**. Critical marks a blocker for the associated feature and quality gate. Expected results are design targets, not actual results.

| ID | Scenario / setup | Expected result | Priority |
| --- | --- | --- | --- |
| TC-001 | Auto-redeem eligible completed booking after staff confirms customer paper signature | One -1 entry; balance decreases once; audit and receipt saved | Critical |
| TC-002 | Redeem with zero sessions | 409; no ledger, balance or success-record change | Critical |
| TC-003 | Retry same committed redemption key and payload, including simultaneous duplicate submission | Same saved response; one deduction | Critical |
| TC-004 | Two different completed, staff-paper-confirmed eligible bookings and keys race for the last session | Exactly one success, one conflict; balance zero; one deduction | Critical |
| TC-005 | Inject persistence failure after an intermediate mutation | Full rollback of the failed purchase/posting/reversal transaction and its success record; earlier completion/signature evidence retained | Critical |
| TC-006 | Call protected API with no token | 401 | Critical |
| TC-007 | Receptionist calls staff/package administration or sales report directly | 403; no mutation | Critical |
| TC-008 | Simultaneously create overlapping appointments for one staff member | At most one booking commits | Critical |
| TC-009 | Execute valid tickets for same Redemption using distinct keys/tickets and concurrent calls | One +1 reversal; second conflict; original unchanged | Critical |
| TC-010 | Redeem a voucher owned by another customer than the booking | Rejected; no mutation | Critical |
| TC-011 | Restore backup into a new database | Data, paper confirmations, drafts, operation/ticket links, relationships and ledger totals match backup manifest | Critical |
| TC-012 | Book at DST gap and repeated local time | Gap rejected; ambiguous time requires explicit offset; valid instants display correctly | Critical |
| TC-013 | Create/update/search customer with invalid and valid input | Stable ID, correct selected profile, bounded search, stale-write rejection, useful validation; no unintended field updates | High |
| TC-014 | Archive customer with existing bookings and vouchers | Selected customer appointment/voucher history and balances retained; other customer records excluded; new booking/purchase rejected | High |
| TC-015 | Adjacent bookings; overlapping reschedule; stale revision | Adjacent accepted; overlap rejected; stale write conflicts | Critical |
| TC-016 | Apply valid/invalid lifecycle transitions; complete without paper confirmation, then confirm signature | Invalid transition rejected; completion alone does not deduct; confirmation triggers automatic deduction | Critical |
| TC-017 | Purchase package then change catalog price and eligibility | Purchased snapshots unchanged; positive opening entry matches entitlement | Critical |
| TC-018 | Retry purchase with same key/payload | One purchased voucher and opening entry; saved response returned | Critical |
| TC-019 | Auto-attempt for ineligible service, incomplete or unsigned booking | Rejected; no deduction | Critical |
| TC-020 | Reuse same key with changed business payload | 409; no second mutation | Critical |
| TC-021 | Submit same booking with new key/different voucher while a deduction remains active | One active deduction only; duplicate conflicts; no replacement without reversal | Critical |
| TC-022 | Each role reverses A then confirms corrected paper usage for B on same completed booking | A restored once, B deducts once automatically; A=5/B=2 from pre-mistake 5/3; original/reversal/ticket/new links visible; one active deduction | Critical |
| TC-023 | Expired, wrong-audience, wrong-issuer, invalid-signature and ID tokens | 401 for each invalid API credential | Critical |
| TC-024 | Valid token belongs to disabled/unmapped staff | 403 under staff-access policy | Critical |
| TC-025 | Summaries against a fixed synthetic fixture across date boundaries | Defined counts/sales match; current balances clearly labeled | High |
| TC-026 | Export with forbidden role and formula-like customer text | Forbidden request denied; allowed CSV safely escaped and mitigated | High |
| TC-027 | Reconcile ledger, then introduce controlled test-only projection discrepancy | Consistent dataset passes; discrepancy detected and reported without silent rewriting | Critical |
| TC-028 | Warm reads: 5 VUs, 60s warm-up, 5 measured min/endpoint, >=1,000 samples | Per-endpoint p95 <500ms and zero unexpected errors; raw samples/environment retained | High |
| TC-029 | Completed booking balance 5: confirm synthetic paper signature; reverse prerequisite order in separate fixture | Neither prerequisite alone deducts; both auto-post -1 leaving 4 without extra click; actor/reference/time/summary linked | Critical |
| TC-030 | Missing/false confirmation, altered signed voucher/service/customer or duplicate confirmations | Invalid/stale binding rejected; changed summary needs fresh confirmation; duplicates never deduct twice | Critical |
| TC-031 | Save/reload Draft; force posting failure and crash between evidence save and posting | Draft retains input without posting; saved completion/signature remains; ReviewRequired or Pending visible; no partial deduction | Critical |
| TC-032 | Drop committed response; query/retry original operation; recheck unchanged failed operation | OutcomeUnknown resolves to original; safe recovery with one deduction per intended operation and at most one active booking deduction | Critical |
| TC-033 | Each role creates/executes ticket; omit ticket/reason or mismatch original | Both allowed without extra approval; Open no balance effect; +1 with ticket/history; invalid rejected | Critical |
| TC-034 | Fail mid-reversal; retry; race another ticket for same original | Failure Open/no partial mutation; success receipt/ticket/ledger/balance atomic; original reversed once | Critical |
| TC-035 | Active staff login/logout; protected operation after local logout | Login works; logout clears local session/token; no authenticated action without fresh credentials | High |
| TC-036 | Invalid page sizes, >31 clinic-date report/export or >10,000 export rows | Reject safely without silent truncation; valid limits return bounded correct authorized data | High |
| TC-037 | Race replacement attempts on B and C for one reversed A, using different keys, tickets and staff | Exactly one replacement commits; no double use; booking active pointer, chain, balances and receipts agree | Critical |
| TC-038 | Try replacement before reversal, after failed reversal, with wrong customer/service/same old voucher/no balance/stale paper confirmation; inject mid-replacement failure | Invalid requests post nothing; committed A reversal survives; failed B transaction rolls back; ticket remains AwaitingReplacement/ReviewRequired; valid retry posts once | Critical |
| TC-039 | After replacing A with B, replay old A deduction/reversal/confirmation and replacement keys; then correct B through a new ticket to C | Old receipts cannot reactivate A or refund twice; B corrects once with its own chain; at most one active deduction, ledger/balance/projection agree | Critical |

## Core schema/API review scenarios

All additional cases remain Not run and require real migrations/API/PostgreSQL where applicable.

| ID | Scenario / setup | Expected result | Priority |
| --- | --- | --- | --- |
| TC-046 | Try cross-customer/voucher, cross-booking confirmation/operation/result pointers; malformed null/type/delta links and forbidden historical deletes | FK/NOT NULL/unique/row-shape checks and use-case validation reject invalid state; no partial committed ledger/audit/receipt | Critical |
| TC-047 | Prepare/retry signing version, complete without changing usage contents, prepare new version and replay old evidence/operation | Preparation has no posting effect; same key increments once; lifecycle preserves summary version; one confirmation/operation per accepted version; superseded cannot post | Critical |
| TC-048 | Evidence commits, automatic posting conflicts/crashes; replay evidence key and recheck explicit old/current operation IDs | Evidence receipt truthfully succeeds with operation link; no false debit; posting failures have no success receipt; old ID never executes different current intent; safe retry once | Critical |
| TC-049 | Race purchase against customer archive and package price/session/eligibility edits | Either allowed coherent snapshot or safe rejection; no mixed old/new entitlement and no unchecked archived-customer purchase | Critical |
| TC-050 | Execute nullable evidence/result-pointer insert order; induce rollback and controlled pointer-vs-unreversed-ledger discrepancy | Valid cyclic references complete within transaction; intermediate failures rollback; FK rejects wrong result owner; reconciliation detects aggregate mismatch and app refuses silent repost/repair | Critical |

## Conditional public-demo scenarios

TC-040–TC-045 apply only if public mode is selected; Critical here blocks public release, not the core local/video fallback. Core TC-001–TC-039 still require real-mode evidence where specified.

| ID | Scenario / setup | Expected result | Priority |
| --- | --- | --- | --- |
| TC-040 | Build valid/invalid modes; navigate all public screens and switch simulated roles | Persistent simulated-backend/role labels; invalid mode stops; visitor cannot enable live access; no real credentials implied | High |
| TC-041 | Capture public network while worker registration fails, handler missing or business requests unhandled | Explicit failure/retry state; no real/local/cloud business API/OIDC or unapproved telemetry request; only expected static assets allowed | Critical |
| TC-042 | Shared synthetic customer/purchase/booking/paper-confirmation/deduction/ticket-replacement/history/dashboard scenarios in public and real modes | Comparable intended UI outcomes and DTOs; differences documented; mock results never counted as real integrity/security evidence | High |
| TC-043 | Mutate/reload in profile A; open independent profile B; deny/full/upgrade storage; open same-origin tabs | Own data persists and independent profile is unchanged; storage failure truthful; shared tabs coordinate; no real-service fallback | Critical |
| TC-044 | Reset with pending requests and another same-origin tab; replay stale-generation responses | Complete deterministic seed restored; ledger/booking/draft/operation/ticket chains coherent; stale writes discarded; only this app's storage affected | Critical |
| TC-045 | Inspect separate public/live artifacts and origins; exercise real identity/API; review optional static release/smoke | Live has no MSW/role impersonation or mock fallback; public has no real secret/business connection; valid worker/deep links, plan/settings and actual release evidence retained before URL claimed | Critical |

For execution, record actual result, pass/fail, commit, fixture, timestamp, evidence and defect ID. Repeat only affected checks after a change, followed by the relevant release regression set.
