# Acceptance Criteria

Version: 0.7 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

All criteria are proposed. Passing them requires executed tests or recorded review evidence; none has been verified by this documentation delivery.

## AC-C — Customer records

- Creating a valid customer returns a stable identifier; missing required display name is rejected.
- Search is paginated and uses only authorized data. Shared phone/email values do not automatically identify one person.
- Profile changes preserve appointment and voucher relationships.
- Archiving retains history and blocks new purchases/bookings for the archived profile in the demo.
- Existing transactions remain inspectable; Owner and Reception may correct only through a linked ticket, without direct balance/history edits.

## AC-B — Appointments

- Creation/rescheduling validates customer, active staff, service and `start < end`.
- Two overlapping appointments for the same staff member cannot both commit, including simultaneous requests and rescheduling.
- A booking ending at 11:00 may be followed by one starting at 11:00.
- Only permitted lifecycle transitions succeed. Cancelled and NoShow are terminal in this demo.
- Completion without paper confirmation leaves balance unchanged (AwaitingSignature). When completion and valid staff-confirmed paper signature both exist, automatically attempt one deduction without another redemption click; show completion and posting outcomes separately.
- Calendar filtering uses Melbourne clinic dates. DST gaps are rejected; ambiguous times require a chosen offset.

## AC-P — Packages and purchases

- Only a Manager changes package definitions or prices.
- A valid purchase snapshots the price, session entitlement and eligible services.
- The voucher, purchase ledger entry, balance and audit change commit together or not at all.
- Later catalog changes do not modify existing purchases.
- Retrying a committed purchase using the same key and payload returns its recorded result without another purchase.
- A sale record is labeled as a recorded purchase, without claiming payment processing or cash receipt.

## AC-V — Redemption and reversal

- Automatic redemption requires active authorized staff, completed booking, staff-confirmed paper signature bound to reviewed customer/booking/service/voucher/quantity, matching ownership, eligible service, active voucher and one available session.
- A successful redemption consumes exactly one session and records actor, appointment, voucher, timestamp and correlation ID.
- Zero balance produces a conflict and no business mutation.
- Repeating the same key/payload returns the original committed result. Reusing a key with a different payload returns a conflict.
- Different keys cannot create two active (un-reversed) deductions for one booking. A linked replacement after committed reversal is permitted under revised DA-07.
- Two different eligible appointments competing for one remaining session produce exactly one successful deduction.
- An injected persistence failure rolls back the balance, ledger, transactional audit and success response record.
- Owner/Manager and Receptionist may both reverse through an Open correction ticket with reason and original link, restoring one original deduction. No extra approval gate applies.
- A second reversal of the same deduction is rejected. After committed reversal, a linked replacement against another correct eligible voucher is allowed; original/reversal/new entries remain inspectable.
- Stored remaining sessions equal the signed ledger sum; ledger rows cannot be edited/deleted through the application.

## AC-A — Identity and access

- Valid staff can log in/out using the selected provider integration.
- Missing, expired, wrongly signed, wrong-issuer or wrong-audience access tokens are rejected with 401.
- Valid identity without required permission returns 403. Unknown/disabled staff are denied even if their token remains cryptographically valid.
- API authorization uses access tokens; an ID token is not accepted as an API credential.
- A booking cannot consume a voucher owned by another customer.
- Receptionists cannot administer staff/packages or read sales reports via direct API calls. Both roles may create tickets and execute linked reversals; either role is rejected if no valid ticket exists.

## AC-R — Reporting and export

- Daily appointments, recorded voucher sales, outstanding sessions and cancellation/no-show counts match fixtures.
- Purchases use purchase date; bookings use appointment date; outstanding sessions are labeled as a current balance snapshot.
- Sales totals are not labeled cash receipts or recognized revenue.
- Manager-only report data remains protected when exported.
- CSV values are escaped and spreadsheet formula injection is mitigated for user-supplied text.

## AC-O — Operations and evidence

- A failed mutation produces a safe error with correlation ID and no token/customer details in logs.
- A backup restores into a clean database and passes relationship, ledger and sample-workflow checks.
- A benchmark identifies commit, environment, dataset, workload and raw results; no target pass is claimed from an unspecified run.
- Release checks retain actual evidence and known defects.

## AC-S — Paper confirmation and automatic posting

- Completed synthetic Facial booking with eligible balance 5: without confirmation remains 5; staff confirmation of customer paper signature automatically posts one -1 and shows 4 without extra deduction click.
- Capture paper reference, confirming staff, server confirmation time and frozen usage summary. Do not fabricate signature images or treat server time as witnessed paper-signing time. Demo references are synthetic.
- Confirmation before completion does not deduct; later completion triggers the same use case. Neither prerequisite alone is sufficient.
- Repeated confirmation/completion and transport/different-key retries create at most one active deduction per booking; an old reversed intent never becomes a new deduction.
- Missing/false confirmation, stale summary, wrong ownership/service, zero balance or inactive actor cannot deduct. Changes to signed customer/service/voucher/quantity require fresh paper confirmation; retain superseded evidence.

## AC-D — Draft, review and uncertain outcomes

- Saving incomplete workflow returns draft ID/revision; reload shows saved values. No purchase/completion/signature/ledger posting is caused by draft save.
- Confirmed deduction failure retains previously saved completion/signature, shows ReviewRequired and safe reason, and leaves no partial balance/ledger/audit/receipt change.
- Crash between evidence save and posting leaves visible Pending work; recheck resumes the same automatic use case without duplicate booking deduction.
- Dropped success response shows OutcomeUnknown. Query/retry original intent/key returns recorded transaction; do not silently create a new key or deduction.
- Unchanged signed details may retry after recheck; changed signed details require fresh confirmation, and changed payload with reused key conflicts. An unsuccessful draft save is never shown as persisted.

## AC-T — Correction tickets

- Both roles create and execute tickets. Creation requires original Redemption and reason and stores customer/booking/voucher/creator/time; Open has no balance effect.
- Detail shows original mistake, reason, state, actor/time and linked reversal. No additional Owner approval or different-person requirement.
- Open -> Reversed, +1 entry, balance, original link, audit and receipt commit together. Injected failure leaves Open and no partial reversal; safe recheck is available.
- Same-key retry returns saved result. Different keys/tickets/concurrent requests cannot reverse an original twice. Preserve deduction, paper evidence and ticket.
- Missing/non-Open ticket, blank reason or mismatched original/customer/voucher is rejected; disabled/unmapped staff remain forbidden.
- After ticket-linked reversal, either role may choose another same-customer eligible voucher and confirm corrected paper usage. Booking remains Completed; normal booking cancellation does not authorize this path. Auto-post new deduction only after valid corrected confirmation; retain original evidence.
- Fixture: wrong voucher A has 5 sessions before mistaken use, correct voucher B has 3. Mistake makes A=4/B=3; ticket reversal makes A=5/B=3; replacement makes A=5/B=2. All three entries and ticket are linked, with exactly one active deduction for the booking.
- Missing/failed reversal, original wrong voucher selected again, other customer, ineligible service, insufficient sessions, stale ticket or paper summary cannot post replacement. Committed reversal remains if replacement fails; show AwaitingReplacement/ReviewRequired and allow safe recheck.
- Duplicate replacement keys, different-key competing vouchers and concurrent staff create one successful replacement per reversed deduction/ticket. Old original/reversal request retries return their historical receipts and never undo the new deduction or deduct again.
- Each successful replacement is a new immutable Redemption with its own operation and links to ticket/reversed original. If subsequently wrong, reverse that specific replacement through a new ticket; chains may continue while at most one active deduction remains.

## AC-DM — Conditional public simulation

Applies only when public mode is selected. Real-mode security/transaction/concurrency/performance criteria above still need real API/PostgreSQL evidence; public simulation cannot satisfy them.

- Public build displays Interactive Demo — Simulated Backend across all screens; Owner/Reception selector is explicitly simulated. Unknown configuration stops startup; visitors cannot switch public artifact to live.
- Share UI/contracts while simulating customer, booking, purchase, paper-signature confirmation, automatic deduction, history/dashboard and ticket reversal/correct-voucher replacement. Use deterministic synthetic fixtures and check expected outcomes; do not claim mock tests prove backend guarantees.
- Independent browser profiles have independent data; same-origin tabs coordinate shared state. Reload restores saved state; unavailable/quota/version storage produces truthful feedback without real API fallback.
- Confirmed Reset Demo invalidates pending operations, restores all related seed records coherently, refreshes same-origin tabs and discards stale-generation writes/responses. Only this app's demo storage is affected.
- Await service-worker/storage readiness before business calls. Missing worker/handler fails explicitly. Network capture shows no real business API, local backend, identity or unapproved telemetry request; normal static asset requests remain allowed.
- Live artifact has no mock interception, mock-role controls or mock fallback, and genuine API/identity paths remain testable. Public artifact contains no real secrets/API credentials; static deployment plan/settings and actual smoke evidence are reviewed before a URL is claimed.

## AC-DB — Reviewed core schema and API integrity

- Prepared usage selection/ticket/summary version and current evidence are persisted. Preparation posts nothing; lifecycle-only changes do not change signed contents. Stale/superseded evidence and wrong operation/ticket IDs cannot post.
- Every ledger type obeys explicit non-null/null shape; composite FKs reject cross-customer/booking/service/voucher/result-owner references. FK/unique/type constraints are exercised against actual migrations, including nullable insert order and restricted historical deletion.
- Balance equals signed ledger total and at most one unreversed booking deduction agrees with active pointer; pointer/ledger mismatch is detected and blocked, never silently repaired. A pointer FK alone is not claimed to enforce the aggregate rule.
- Evidence-save receipt and posting receipt are different facts. A committed evidence action returns its evidence/operation IDs, even if posting is pending/review. Original-key replay returns historical receipt; explicit GET/recheck operation ID cannot silently select another intent.
- Customer/archive and package/catalog races produce one coherent allowed purchase/snapshot or a safe rejection, never mixed entitlement. Claim/receipt plus the specific action share a transaction; claim failure/posting failure cannot leave a fabricated success.
- These are planned implementation checks (TC-046–050) and existing concurrency/recovery checks; document review alone is no test pass.
