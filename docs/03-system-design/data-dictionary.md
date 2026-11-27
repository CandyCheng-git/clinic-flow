# Data Dictionary and Data Management

Version: 0.7 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Conventions

Proposed relational schema: UUID primary keys, foreign keys, UTC instants (`timestamptz`), explicit status values, and AUD amounts stored as `numeric(12,2)`. Use `CreatedAt/CreatedBy` and appropriate `UpdatedAt/UpdatedBy` fields on mutable records. Do not use floating-point money. Preserve clinic time zone `Australia/Melbourne` separately from appointment instants.

## Entity fields and constraints

| Entity | Principal fields | Proposed rules / constraints |
| --- | --- | --- |
| Customers | Id, DisplayName, Phone?, Email?, IsArchived, Revision, audit fields | DisplayName required; phone/email not globally unique; no medical notes |
| Staff | Id, DisplayName, IsActive, CanPerformServices, IdentityIssuer?, IdentitySubject?, Role?, Revision | Unique non-null issuer/subject pair; Manager/Receptionist application roles only |
| Services | Id, Name, DefaultDurationMinutes, IsActive, Revision | Positive duration; archive rather than delete used services |
| Bookings | Id, CustomerId, StaffId, ServiceId, StartsAt, EndsAt, Status, SelectedCustomerVoucherId?, PreparedCorrectionTicketId?, UsageSummaryRevision, CurrentSignatureConfirmationId?, ActiveRedemptionTransactionId?, Revision, audit fields | End after start; positive row Revision; nonnegative UsageSummaryRevision; nullable selection/confirmation/active pointers belong to this customer/booking. FK validates identity, transaction validates current/unreversed state |
| VoucherPackages | Id, Name, SessionCount, Price, Currency, IsActive, Revision, audit fields | Sessions > 0; price >= 0; Currency = AUD |
| PackageServices | PackageId, ServiceId | Composite primary key; at least one eligibility entry at use-case boundary |
| CustomerVouchers | Id, CustomerId, PackageId, PurchasedAt, PurchasePrice, Currency, PurchasedSessions, RemainingSessions, ExpiresOn?, Status | Snapshot price/quantity; balance >= 0; no expiry enabled in demo |
| VoucherServiceSnapshots | CustomerVoucherId, ServiceId | Composite primary key; purchase-time eligibility retained |
| VoucherTransactions | Id, CustomerVoucherId, Type, SessionDelta, BookingId?, DeductionOperationId?, SignatureConfirmationId?, ReversesTransactionId?, ReplacesTransactionId?, CorrectionTicketId?, ActorStaffId, Reason?, OccurredAt, CorrelationId | Signed delta; immutable via application; type-dependent shape constraints |
| PaperSignatureConfirmations | Id, BookingId, CustomerId, ServiceId, CustomerVoucherId, Quantity, ReviewedSummaryRevision, PaperReference, ConfirmedByStaffId, ConfirmedAt, ReportedPaperSignedAt?, SupersedesId? | Immutable accepted summary; unique BookingId/ReviewedSummaryRevision, one confirmation per prepared version; quantity 1; no image; confirmed time separate from paper signing time |
| DeductionOperations | Id, BookingId, SignatureConfirmationId, CorrectionTicketId?, ReplacesTransactionId?, InitiatedByStaffId, CreatedAt, UpdatedAt, IntentHash, State, LastSafeErrorCode?, TransactionId?, Revision | Unique SignatureConfirmationId; Pending/ReviewRequired/Superseded/Redeemed/Reversed; Superseded is unposted only; identity fields immutable; posted intent never posts again |
| WorkflowDrafts | Id, CreatorStaffId, WorkflowType, BoundedInput, Revision, CreatedAt, UpdatedAt | Allow-listed input only; creator-only access; no signature image/token; save never posts business mutation |
| CorrectionTickets | Id, OriginalTransactionId, CustomerId, BookingId, CustomerVoucherId, Reason, Status, CreatedByStaffId, CreatedAt, ExecutedByStaffId?, ExecutedAt?, ReversalTransactionId?, ReplacementState, ReplacementTransactionId? | Open/Reversed status; separate replacement progress; both roles execute; unique reversal/replacement links; one successor per original/ticket |
| AuditLogs | Id, ActorStaffId?, EntityType, EntityId, Action, Reason?, OccurredAt, CorrelationId | Append-only application behavior; bounded safe metadata; no full customer payload |
| IdempotencyRecords | Id, ActorStaffId, Operation, Key, RequestHash, State, HttpStatus?, ResponseBody?, CreatedAt, UpdatedAt | Unique actor/operation-stage/key; InProgress claim has null status/body inside transaction; Committed has immutable success receipt/resource links; claim and action commit together; retained for dataset lifetime |

`ExpiresOn` is a clinic-local date only if a future policy explicitly defines it that way. It stays null in this MVP. A package price is a sale-record amount; no payment-card or bank details are stored.

## Ledger shape and nullable fields

All core IDs/FKs and type/delta/actor/time/correlation fields are NOT NULL unless marked optional. UUID PKs, numeric AUD and integer session fields retain the conventions above. CHECK does not replace NOT NULL. Purchase/redemption/reversal are the only enabled ledger types; explicit branches must state required fields and IS NULL for every forbidden link.

| Type | Delta | BookingId / operation / confirmation | ReversesTransactionId | ReplacesTransactionId / ticket |
| --- | --- | --- | --- | --- |
| Purchase | Positive; equals purchased entitlement | All null | Null | Both null |
| Initial Redemption | -1 | All required | Null | Both null |
| Replacement Redemption | -1 | All required | Null | Both required |
| Reversal | +1 | All null | Required | Replaces null; ticket required |

Use a unique partial index on CustomerVoucherId for Type=Purchase. Unique non-null DeductionOperationId and SignatureConfirmationId prevent duplicate posting of an intent/evidence. Unique non-null ReversesTransactionId and ReplacesTransactionId prevent double restoration and multiple successors. CorrectionTicketId is unique separately for Type=Reversal and Type=Redemption; one ticket may link to one of each. Ledger BookingId is indexed but NOT globally unique.

## Core integrity enforcement matrix

| ID / rule | Database constraint / reference | Transaction / reconciliation responsibility |
| --- | --- | --- |
| DB-001 Required values and bounds | PK/FK/NOT NULL, named allowed-state and row CHECKs; purchased sessions >0; 0 <= remaining <= purchased; prices >=0; AUD; start < end; positive revisions | Validate request limits and return safe errors; reject unsupported ledger types |
| DB-002 Purchased ownership and signed service | Candidate UNIQUE CustomerVouchers(Id, CustomerId); confirmation/voucher composite FK matches customer; confirmation(BookingId,CustomerId,ServiceId) references matching booking candidate key; confirmation(voucher,service) references purchased service snapshot | Freeze identity/service after signing/history; do not merge customers by contact; validate chosen voucher/current state |
| DB-003 Prepared/current signing version | Unique confirmation(BookingId,ReviewedSummaryRevision); operation SignatureConfirmationId unique FK; booking current-confirmation composite FK includes booking ID and prepared version; selected-voucher/customer composite FK | UsageSummaryRevision increments only on signing-summary preparation, not lifecycle/posting. Current pointer/version must agree; clear on new prepare, supersede old unposted intent, preserve evidence |
| DB-004 Ledger-operation/signature agreement | Ledger composite FK (DeductionOperationId,BookingId,SignatureConfirmationId) references matching UNIQUE operation tuple; ledger(signature,voucher) references matching UNIQUE confirmation tuple | Check current evidence/summary, active actor and initial/replacement context before posting; historical posted intent is read before mutable validation |
| DB-005 One active booking deduction | Booking active-pointer composite FK (ActiveRedemptionTransactionId,Id) -> ledger(Id,BookingId), matching non-partial UNIQUE candidate key; unique non-null active pointer | Pointer alone does not enforce one unreversed ledger row. All writers lock booking, check pointer/history and atomically update ledger/pointer; independently reconcile at most one unreversed row and matching pointer |
| DB-006 Reversal/predecessor/ticket | FKs plus unique reversal/predecessor links and type-specific ticket indexes; ticket original composite reference matches booking/voucher; original must be a Redemption | Check same original voucher/+1, already-reversed state, successor same booking/customer and different eligible voucher; one correct linear chain; no cyclic/self links |
| DB-007 Balance and companion rows | Integer bounds, row CHECK and FK/unique protections above | Aggregate ledger sum, exactly one opening Purchase, service-snapshot minimum and receipt/audit companions require coordinated writes/reconciliation, not row CHECKs |
| DB-008 Retry and stale edits | Unique actor/operation-stage/key, unique intent/evidence/predecessor references; optimistic Revision fields on mutable entities | Request hash includes route target and normalized payload; authorize replay; reject changed payload/stale revision; failed posting creates no success receipt |
| DB-009 Immutable and historical records | Restricted parent deletion; application database role has no routine UPDATE/DELETE on ledger/evidence; migration/admin role separate | Corrections append entries; preserve old receipt/evidence. Generic audit EntityType/EntityId is not an enforceable polymorphic FK |
| DB-010 Catalog/archiving races | Historical FK/snapshot constraints | Purchase locks customer status and package snapshot source; catalog eligibility writers follow same package locking protocol. Recheck state after locks; no mixed old price/new entitlement |

Declare explicit matching non-partial UNIQUE candidate keys before composite FKs. Examples include Bookings(Id,CustomerId,ServiceId), PaperSignatureConfirmations(Id,BookingId), PaperSignatureConfirmations(Id,CustomerVoucherId), DeductionOperations(Id,BookingId,SignatureConfirmationId), and VoucherTransactions(Id,BookingId) plus the ticket-original matching tuple. Also declare the specific candidate keys needed for operation-result ownership, ticket-result ownership, same-voucher reversal and same-booking predecessor/supersession/current-version links. Current confirmation FK is (CurrentSignatureConfirmationId,Id,UsageSummaryRevision) -> confirmation(Id,BookingId,ReviewedSummaryRevision); prepared ticket FK is (PreparedCorrectionTicketId,Id) -> ticket(Id,BookingId). Result pointers use (result ID, owner ID) composite FKs to the ledger’s corresponding owner column, with type/state verified transactionally. A partial unique index is not a composite-FK target. Reference type/current state and cross-row totals still require the checks above.

Reverse result pointers start null. Insert evidence then its operation and update booking pointer in one transaction. Insert ledger then set operation/ticket/booking result pointers in one transaction. This avoids requiring a non-null circular reference before its target exists. Composite nullable references use explicit row-shape checks where all-or-none optional components are required; do not accidentally rely on SQL null behavior.

## Composite FK map for migration planning

Each target tuple needs a matching non-partial UNIQUE key (or existing PK) before the FK is created. Nullable first-column pointers intentionally skip the reference while unset; ledger row-shape checks require all non-null parts for that type. Use restricted historical deletion; cross-row type/current-state checks remain transactional.

| Source tuple | Target tuple | Purpose |
| --- | --- | --- |
| Bookings(SelectedCustomerVoucherId,CustomerId) | CustomerVouchers(Id,CustomerId) | Prepared selection belongs to customer |
| Bookings(PreparedCorrectionTicketId,Id) | CorrectionTickets(Id,BookingId) | Prepared ticket belongs to booking |
| Bookings(CurrentSignatureConfirmationId,Id,UsageSummaryRevision) | PaperSignatureConfirmations(Id,BookingId,ReviewedSummaryRevision) | Current evidence matches prepared version |
| Bookings(ActiveRedemptionTransactionId,Id) | VoucherTransactions(Id,BookingId) | Active pointer references this booking’s Redemption |
| PaperSignatureConfirmations(BookingId,CustomerId,ServiceId) | Bookings(Id,CustomerId,ServiceId) | Frozen customer/service matches booking |
| PaperSignatureConfirmations(CustomerVoucherId,CustomerId) | CustomerVouchers(Id,CustomerId) | Signed voucher belongs to customer |
| PaperSignatureConfirmations(CustomerVoucherId,ServiceId) | VoucherServiceSnapshots(CustomerVoucherId,ServiceId) | Signed service is in purchased eligibility |
| PaperSignatureConfirmations(SupersedesId,BookingId) | PaperSignatureConfirmations(Id,BookingId) | Prior evidence is for same booking; lower version checked in use case |
| DeductionOperations(SignatureConfirmationId,BookingId) | PaperSignatureConfirmations(Id,BookingId) | Operation evidence belongs to booking |
| VoucherTransactions(DeductionOperationId,BookingId,SignatureConfirmationId) | DeductionOperations(Id,BookingId,SignatureConfirmationId) | Posted intent and evidence agree |
| VoucherTransactions(SignatureConfirmationId,CustomerVoucherId) | PaperSignatureConfirmations(Id,CustomerVoucherId) | Debit uses the signed voucher |
| VoucherTransactions(ReversesTransactionId,CustomerVoucherId) | VoucherTransactions(Id,CustomerVoucherId) | Reversal restores original voucher |
| VoucherTransactions(ReplacesTransactionId,BookingId) | VoucherTransactions(Id,BookingId) | Replacement predecessor belongs to booking |
| CorrectionTickets(OriginalTransactionId,BookingId,CustomerVoucherId) | VoucherTransactions(Id,BookingId,CustomerVoucherId) | Ticket matches original deduction |
| CorrectionTickets(CustomerVoucherId,CustomerId) | CustomerVouchers(Id,CustomerId) | Ticket customer matches original voucher |
| DeductionOperations(TransactionId,Id) | VoucherTransactions(Id,DeductionOperationId) | Result points to own posted deduction |
| CorrectionTickets(ReversalTransactionId,Id) | VoucherTransactions(Id,CorrectionTicketId) | Result belongs to this ticket; Reversal type checked |
| CorrectionTickets(ReplacementTransactionId,Id) | VoucherTransactions(Id,CorrectionTicketId) | Successor belongs to this ticket; Redemption type checked |

## Signing preparation and state

Bookings.SelectedCustomerVoucherId and UsageSummaryRevision are the persisted source for the API reviewedSummaryRevision. Preparing a new summary is idempotent, locks the booking, validates initial or reversed-ticket context and selected ownership/service, saves SelectedCustomerVoucherId/PreparedCorrectionTicketId, then increments UsageSummaryRevision/Revision and clears CurrentSignatureConfirmationId. It has no ledger effect and cannot run while an active deduction exists. Current unposted operations become Superseded; their evidence remains immutable. For the matching prepared replacement ticket, new preparation sets AwaitingReplacement, accepted confirmation sets Pending, and only its current operation can later set ReviewRequired/Replaced. Prior completed tickets stay historical. Completion/posting changes the row Revision but not UsageSummaryRevision.

Accepted confirmation copies customer/service/voucher/quantity=1 and that prepared version; server resolves actor/time and optional SupersedesId to an earlier confirmation of the same booking. Current pointer, one operation, evidence-save receipt and business audit commit together. Same-version duplicate cannot create another operation. Only the current prepared version can post. A new version may use the same voucher after a non-posted error, but replacement after committed reversal must use another voucher.

Ticket Status is Open/Reversed. ReplacementState is NotStarted before reversal, AwaitingReplacement after reversal, Pending/ReviewRequired for the current replacement operation, or Replaced on success. Failed/superseded operations cannot overwrite a newer current ticket outcome. Superseded operations have no TransactionId; Redeemed/Reversed operations retain their original TransactionId. Current booking progress is a projection, not the operation state enum: AwaitingSignature/Pending/ReviewRequired/Redeemed/Reversed. OutcomeUnknown is client uncertainty only.

## Initial index candidates

Customer search keys based on chosen normalization; Bookings by StaffId/StartsAt and CustomerId/StartsAt; CustomerVouchers by CustomerId; VoucherTransactions by CustomerVoucherId/OccurredAt; audit by entity/time. Add uniqueness indexes described above. Measure real query plans and write overhead before adding more indexes.

## Synthetic datasets

| Data | Small development set | Portfolio-scale target |
| --- | --- | --- |
| Customers | 20 | 1,000 |
| Staff | 3 | 8 |
| Bookings | 100 | 10,000 |
| Voucher purchases | 30 | 1,500 |
| Voucher ledger entries | 80 | 5,000 |

These are fixture targets, not clinic statistics or generated results. The generator must preserve eligibility, ledger sums, valid state transitions, ownership and scheduling invariants. Fix a random seed, generator version and reference date. Generate normal, edge, invalid-request and performance fixtures separately; do not insert invalid data into constrained tables just to test rejection.

## Existing identifiers — pending design, not schema

Paper customer files have an existing customer ID (Candy, 2026-11-27). Whether POS uses that same ID is unconfirmed. `Customers.Id` remains the proposed ClinicFlow internal UUID. Evaluate separate source-labelled references for paper and POS identifiers after validating their relationship; this note does not add fields, indexes or endpoints to the baseline schema.

Treat source identifiers as strings during analysis so leading zeros and letters are retained. Confirm uniqueness, case/whitespace rules, duplicate handling and the authoritative mapping before deciding constraints. Do not infer identity from an equal-looking number or merge customers solely on names/phone numbers. Import and opening-balance functionality remain deferred.

## Future migration

Before any authorized import, inventory sources, map fields, clean duplicates, define identifiers, dry-run the import, reconcile counts and balances, and rehearse rollback. Real customer data and clinic documents are excluded from this portfolio. No authorization for import is recorded.

ReplacementState is NotStarted before reversal, AwaitingReplacement after committed reversal, Pending/ReviewRequired during new posting, or Replaced on success. Subsequent correction gets its own ticket; prior links remain historical. Reconcile ledger sums, booking pointer vs unreversed Redemptions, linear predecessor/successor links and one successor per reversed deduction.

## Review references and evidence

Reviewed as schema/transaction design on 27 November 2026. PostgreSQL explains row CHECK/null limitations and foreign-key targets in [constraints](https://www.postgresql.org/docs/18/ddl-constraints.html), and explicit locking/isolation in [locking](https://www.postgresql.org/docs/18/explicit-locking.html) and [isolation](https://www.postgresql.org/docs/18/transaction-iso.html). These references support the enforcement split; they do not prove this unimplemented schema. Pin actual compatible server/provider versions during setup and run TC-046–050 plus existing concurrency/rollback cases against that real database.
