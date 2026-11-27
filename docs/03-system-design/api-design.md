# API Design

Version: 0.7 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Contract status

Proposed endpoint inventory and behavior. This Markdown document is not an executable OpenAPI specification. Generate/review the actual contract alongside implemented DTOs and endpoints.

## Endpoint inventory

All `/api` resources require authenticated active staff. `Both` means Manager or Receptionist within the single clinic. The server must enforce permissions independently of UI visibility.

| Method | Endpoint | Access | Purpose |
| --- | --- | --- | --- |
| GET / POST | `/api/customers` | Both | Search / create |
| GET / PUT | `/api/customers/{id}` | Both | Profile / update |
| POST | `/api/customers/{id}/archive` | Both | Archive |
| GET | `/api/customers/{id}/vouchers` | Both | Balances and purchase history |
| GET / POST | `/api/bookings` | Both | List calendar / create |
| GET | `/api/bookings/{id}` | Both | Booking, selected signing summary/version, current confirmation, active deduction and current operation links |
| PUT | `/api/bookings/{id}` | Both | Reschedule Scheduled/Confirmed booking |
| PUT | `/api/bookings/{id}/usage-summary` | Both | Prepare selected voucher and optional reversed-ticket context for paper review; no deduction |
| POST | `/api/bookings/{id}/confirm` | Both | Confirm |
| POST | `/api/bookings/{id}/start` | Both | Mark InProgress |
| POST | `/api/bookings/{id}/cancel` | Both | Cancel with reason |
| POST | `/api/bookings/{id}/no-show` | Both | Mark NoShow |
| POST | `/api/bookings/{id}/complete` | Both | Save completion; auto-attempt deduction when paper confirmation exists |
| GET | `/api/services` | Both | Active catalog |
| POST / PUT | `/api/services` / `/api/services/{id}` | Manager | Maintain services |
| GET | `/api/staff` | Both | Minimal schedulable staff list |
| POST / PUT | `/api/staff` / `/api/staff/{id}` | Manager | Administer staff and active status |
| GET | `/api/voucher-packages` | Both | Available package definitions |
| POST / PUT | `/api/voucher-packages` / `/api/voucher-packages/{id}` | Manager | Maintain packages |
| POST | `/api/vouchers/purchase` | Both | Record a purchase |
| POST | `/api/bookings/{id}/paper-signature-confirmations` | Both | Initial paper confirmation; auto-attempt when completed; cannot bypass correction history |
| POST | `/api/correction-tickets/{id}/replacement-confirmations` | Both | After committed reversal confirm corrected paper usage for another voucher; auto-attempt replacement |
| POST | `/api/deduction-operations/{id}/recheck` | Both | Resume one explicitly identified current intent; old terminal intent returns history, never the latest different intent |
| GET | `/api/deduction-operations/{id}` | Both | Authorized current posting outcome; minimal safe receipt |
| GET / POST | `/api/workflow-drafts` | Both | List own saved drafts / save incomplete workflow without posting |
| GET / PUT | `/api/workflow-drafts/{id}` | Both, creator only | Reopen/update own draft with revision |
| GET | `/api/vouchers/{id}/transactions` | Both | Ledger history |
| GET / POST | `/api/correction-tickets` | Both | Inspect/create tickets linked to original Redemption and reason |
| GET | `/api/correction-tickets/{id}` | Both | Ticket, original mistake, reversal and audit links |
| POST | `/api/correction-tickets/{id}/reverse` | Both | Execute Open ticket reversal without extra approval |
| GET | `/api/dashboard/summary` | Both | Operational counts; no sales totals |
| GET | `/api/reports/voucher-sales` | Manager | Recorded sales summary |
| GET | `/api/reports/outstanding-sessions` | Manager | Current balance snapshot |
| GET | `/api/reports/export` | Manager | Bounded authorized CSV export |

Separating operational counts from Manager-only sales data prevents accidental disclosure through a shared dashboard DTO. Return only minimal staff data to Receptionists; identity administration fields are Manager-only.

## Request conventions

- Use request/response DTOs, allow-listed fields, bounded strings and server-side validation.
- Lists default to pageSize 50, maximum 100, with stable ID sorting tie-breaker; invalid sizes rejected. Report/export date ranges are inclusive-start/exclusive-end and at most 31 clinic dates; exports max 10,000 rows, reject oversize rather than truncate.
- Persist explicit statuses and timestamps; accept appointment instants with offsets and validate clinic-local user input.
- Use optimistic revision checks for mutable profile/package/booking edits; reject stale writes with 409.
- Require Idempotency-Key for purchase, usage-summary preparation, completion, paper confirmation, operation recheck, ticket creation, ticket reversal and replacement confirmation. Posting uses the stable persisted booking/confirmation intent. Retain the original key for same-intent retry; changed payload conflicts.
- Review contract compatibility when changing DTOs; do not silently reinterpret existing fields.

## Core request-to-entity contract

All real requests require active staff and role/resource permission. Actor, timestamps, ledger type/delta, balances, active pointer, operation/ticket states and result IDs are server-controlled; reject them as writable input. Whole-number usage is fixed at one. Examples below are proposed DTO contracts, not working endpoints.

| Route/action | Reviewed input | Persisted source and response |
| --- | --- | --- |
| Purchase | customerId, packageId, reviewedPackageRevision, Idempotency-Key | Under customer/package guards copy purchase price/session/service snapshots; 201 purchased voucher/opening transaction/receipt. No payment processing |
| Prepare usage summary | selectedVoucherId, bookingRevision, correctionTicketId?; key | Lock booking; no active deduction. Initial history-free context or already-reversed matching ticket. Save selected voucher/prepared ticket, increment UsageSummaryRevision/Revision, clear current confirmation and supersede older unposted intent. 200 reviewed summary, both versions and links; no signature/completion/ledger effect |
| Complete booking | bookingRevision; key | Persist permitted lifecycle transition and receipt; 200 booking/completion saved and operation link if present. Automatically invoke current posting only if matching confirmation also exists |
| Initial paper confirmation | confirmed=true, paperReference, bookingRevision, reviewedSummaryRevision, selectedVoucherId; optional reportedPaperSignedAt; key | selectedVoucherId must equal prepared selection and ticket context must be null. Freeze prepared customer/service/voucher/quantity/version; save evidence/operation/current pointer/audit/receipt; 201 evidenceSaved=true, confirmationId, operationId and status URL |
| Replacement confirmation | Same evidence fields, ticket from route; key | Ticket must match PreparedCorrectionTicketId and committed original reversal; correct selected voucher differs from original. Same evidence-save response; new operation stores ticket/predecessor context |
| Create ticket | originalTransactionId, reason; key | Derive customer/booking/voucher from original, require eligible current unreversed Redemption. 201 Open ticket; no balance effect |
| Reverse ticket | ticket from route, reason already on ticket; key | 200 only after atomic +1, active-pointer clear, operation/ticket/audit/receipt commit; result links preserved |
| Recheck operation | operation from route; key, no replacement payload | Resolve exactly that immutable intent, authorize current actor, replay historical posted/reversed outcome first. Only current eligible unposted intent may execute; superseded cannot post. 200 committed/historical outcome; 409 not-ready or confirmed posting conflict with operation URL; failure has no posting success receipt |
| Read booking/operation/ticket | Authorized route IDs | 200 current projections plus immutable result/history links. Current balance is distinguished from balance-at-original-receipt |
| Save workflow draft | workflowType, allow-listed incomplete input; revision on update | Creator-owned draft ID/revision; no posting, signing, booking completion or balance mutation |

Preparing signing details precedes customer paper review. Completion changes booking Revision but not UsageSummaryRevision; a client can refresh the row version without changing unchanged signed contents. Same-key evidence retry resolves before stale-row checks; current resource state is read separately. A new prepared version can supersede old failed evidence but cannot change a posted original; use the ticket correction path after reversal.

## Evidence save versus posting result

Paper confirmation atomically saves evidence, one operation, current pointer, transactional audit and its immutable receipt. It then automatically attempts posting in a separate transaction when completion also exists. Return 201 for successful evidence save even if posting is Pending/ReviewRequired; the stable receipt contains resource IDs and operation status URL, not a claim of deduction success. GET that operation to show current posting state/transaction/server balance. Completion similarly returns its committed fact, not an invented successful debit.

400/409 before evidence acceptance means that new evidence was not saved (e.g. missing/false confirmation, stale prepared version, wrong selected voucher/context or changed-key payload). A later posting validation conflict is on the operation, not failure of already-committed evidence. Network/5xx may leave earlier evidence committed; retry original key and fetch its operation. This replaces the earlier ambiguous proposal that returned an evidence-save 409 after partial success.

Identical accepted confirmation for a prepared version resolves to the existing evidence/operation; different content for that occupied version is a conflict requiring explicit new preparation. Superseded or non-current evidence cannot authorize posting. Initial routes cannot bypass ticket context after any historical deduction.

Draft save/update/list/get is creator-only. Correction ticket detail is visible to authorized active staff of either role; neither role needs extra Owner approval to reverse/replace. No raw ledger or direct balance-edit endpoint exists.

## Errors and idempotency

| Status | Meaning |
| --- | --- |
| 400 | Invalid input or malformed request |
| 401 | Missing or invalid API credential |
| 403 | Authenticated identity lacks staff access or permission |
| 404 | Authorized resource cannot be found |
| 409 | State, duplicate-operation, overlap, balance, stale-write or key/payload conflict |
| 429 | Request limit exceeded |
| 500 | Unexpected server failure with safe public details |

Use a consistent problem body with a stable application code and correlation ID. Do not expose SQL, stack traces, connection strings or customer details. An authorized committed same-key/same-payload retry returns its immutable saved receipt before mutable version/balance checks. Its balance/time belongs to that historical event; fetch current operation/resource state separately. Changed payload conflicts. Evidence receipts and posting receipts are separate operation stages. Failed/not-ready posting creates no success receipt, so the same intent/key can recover later. Different-key business duplicates resolve to the existing accepted evidence or conflict, never a second effect.

See [LLD](LLD.md) for atomic response persistence and simultaneous key claims.

## Correction replacement and recovery

After ticket reversal commits, prepare usage-summary with its correctionTicketId and another eligible same-customer voucher. Confirm the reviewed paper details through that ticket's replacement-confirmations route. Route ticket, prepared context, original booking/voucher, committed reversal and new operation predecessor must agree. Posting updates -1/new balance/active pointer/operation/ticket successor/audit/receipt atomically.

Reversal keeps booking Completed. Ticket Status=Reversed is separate from ReplacementState=AwaitingReplacement/Pending/ReviewRequired/Replaced. New failure preserves old +1 restoration. Reads expose original, reversal, current replacement operation and new transaction links. GET booking provides current recovery links; POST recheck always targets an explicit operation ID, not whichever intent happens to be latest. Old original/reversal keys never reactivate or refund a successor.

The inventory remains proposed Markdown, not a complete executable OpenAPI specification. The first-slice contract and entity mapping are reviewed here; full DTO schemas, report pagination examples and OpenAPI checks remain part of R09/implementation.

## Optional mock contract reuse

The endpoint inventory describes the real API and its staff/security requirements. Optional MSW handlers simulate the supported demo subset using the same DTO expectations; they do not create hosted API endpoints or relax real policies. Simulated role selection supplies no real credential. Run shared fixture outcomes for paper confirmation, ticket reversal/replacement and reporting to detect drift. Unhandled business requests fail rather than reaching a real service. See [demo strategy](../07-deployment/demo-strategy.md).

Core contract review evidence: request-to-entity mapping and AC-DB are covered by planned TC-046–050; TC-003/004/009/022/029/032/037–039 remain the retry/concurrency/correction baseline. All are Not run. Full API contract/traceability completion R09 awaits remaining DTO/OpenAPI/report detail.
