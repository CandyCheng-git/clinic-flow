# Entity Relationship Design

Version: 0.7 | Updated: 27 November 2026

Status: Core relationship/design review completed — proposed schema; no migrations or database verification

Reviewed against SRS v0.5, HLD v0.6 and DEC-20261127-02–07. The model supports paper confirmation -> automatic deduction -> ticket reversal -> another correct voucher, while retaining one active deduction and immutable history. Optional mock storage is not this PostgreSQL schema. See [data dictionary](data-dictionary.md), [LLD](LLD.md) and [API](api-design.md).

## Core relationships

~~~mermaid
erDiagram
    Customers ||--o{ Bookings : books
    Staff ||--o{ Bookings : performs
    Services ||--o{ Bookings : provides
    Customers ||--o{ CustomerVouchers : owns
    VoucherPackages ||--o{ CustomerVouchers : defines
    VoucherPackages ||--o{ PackageServices : allows
    Services ||--o{ PackageServices : listed
    CustomerVouchers ||--|{ VoucherServiceSnapshots : snapshots
    Services ||--o{ VoucherServiceSnapshots : eligible
    CustomerVouchers ||--|{ VoucherTransactions : records
    Bookings o|--o{ VoucherTransactions : redemption_history
    CustomerVouchers o|--o{ Bookings : selected_for_review
    VoucherTransactions o|--o| Bookings : active_redemption
    Bookings ||--o{ PaperSignatureConfirmations : has
    Customers ||--o{ PaperSignatureConfirmations : identifies
    Services ||--o{ PaperSignatureConfirmations : records_service
    CustomerVouchers ||--o{ PaperSignatureConfirmations : confirms_usage
    Staff ||--o{ PaperSignatureConfirmations : confirms
    PaperSignatureConfirmations o|--o| Bookings : current_confirmation
    PaperSignatureConfirmations ||--|| DeductionOperations : initiates
    Bookings ||--o{ DeductionOperations : tracks
    Staff ||--o{ DeductionOperations : initiates_as_staff
    DeductionOperations o|--o| VoucherTransactions : posts_redemption
    PaperSignatureConfirmations o|--o| VoucherTransactions : authorizes_redemption
    VoucherTransactions o|--o| VoucherTransactions : reversed_by
    VoucherTransactions o|--o| VoucherTransactions : replaced_by
    VoucherTransactions ||--o{ CorrectionTickets : original_for
    CorrectionTickets o|--o{ DeductionOperations : replacement_intents
    CorrectionTickets o|--o| Bookings : prepared_correction
    CorrectionTickets o|--o{ VoucherTransactions : reversal_and_successor
    Staff ||--o{ VoucherTransactions : posts
    Staff ||--o{ CorrectionTickets : creates
    Staff o|--o{ CorrectionTickets : executes
    Staff ||--o{ WorkflowDrafts : owns
    Staff ||--o{ IdempotencyRecords : scopes
    Staff o|--o{ AuditLogs : acts
~~~

Multiplicity describes valid committed state, not every intermediate insert. A package may be a draft without service links, but cannot be purchased without at least one eligible service. A purchased voucher commits with one opening Purchase and at least one service snapshot. Each accepted paper confirmation commits with exactly one operation; that operation posts zero or one Redemption. Unique/FK constraints enforce maximums/references; application transactions enforce required companion rows and aggregate invariants.

## Nullable links and correction history

| Link | Reviewed cardinality / meaning |
| --- | --- |
| Customer -> purchased voucher | One customer owns many purchases; catalog definition is not the customer's balance |
| Booking -> selected voucher | Zero/one current signing selection; matches booking customer and service; preparing selection does not deduct |
| Booking -> paper confirmations | Many immutable versions, at most one accepted confirmation per UsageSummaryRevision; current pointer zero/one |
| Booking -> operations | Many versioned intents; superseded unposted intents cannot execute; replay of posted/reversed intent returns history |
| Booking -> ledger | Zero/many Redemption rows over correction history; Purchase/Reversal have no direct BookingId |
| Booking -> active Redemption | Zero/one current pointer; at most one unreversed deduction is a transactional invariant, not a pointer-uniqueness guarantee by itself |
| Redemption -> reversal | Zero/one opposite +1 entry, same voucher, linked through unique ReversesTransactionId |
| Reversed Redemption -> successor | Zero/one new -1 using another eligible same-customer voucher, linked through unique ReplacesTransactionId |
| Original Redemption -> tickets | Zero/many ticket records may exist, but original can reverse once and have one successful successor across them |
| Ticket -> result ledger | At most one Reversal and one replacement Redemption; separate type-specific uniqueness, not global uniqueness of CorrectionTicketId |
| Audit -> business entity | EntityType/EntityId is a safe audit reference, not a polymorphic database FK; business provenance also uses typed FKs on ledger/tickets |

## Referential and transaction boundaries

All real parent references have FKs and restricted historical deletion. Composite FKs match customer/booking/service/voucher identity where practical; runtime validation remains necessary for active states, current revisions, reversal type/history and operation/ticket state. Reverse references such as booking active pointer, operation result and ticket result begin null, then point to inserted ledger rows within the same transaction; do not require a new row to reference itself before it exists.

Preparing a signing summary saves selected voucher/version without a ledger effect. Paper confirmation saves immutable evidence, one operation and current-confirmation pointer together. Posting saves ledger/balance/active pointer/operation/audit/receipt together; reversal clears the active pointer and restores once; replacement is a separate transaction and cannot roll back an earlier committed reversal. Explicit request idempotency and booking/voucher locking are mandatory for all writers. No global unique constraint on ledger BookingId.

Balance = signed ledger sum, one active deduction, valid correction chain and existence of one opening Purchase are cross-row invariants. Enforce via coordinated writes, appropriate unique/FK/check constraints and reconciliation; a simple row CHECK cannot prove those aggregates. See the enforcement matrix in the dictionary and planned TC-046–050.

## Review record — 27 November 2026

- Added missing confirmation-to-voucher/customer/service, operation-to-booking/actor and booking current-selection/confirmation/result relationships.
- Corrected optionality for operations and signed evidence: an operation or confirmation authorizes no more than one ledger deduction; purchase/reversal have no direct booking link.
- Separated type-specific ticket links and reversed history from the active deduction projection; retained multiple tickets but only one successful reversal/successor per original.
- Mapped signing-summary revisions and every core API write to persisted fields in the dictionary. No raw database FK is claimed for generic audit EntityId.

R05 records completion of this ERD/dictionary document review. R06 records the linked transaction-design review in LLD. API first-slice notes are reviewed, but full OpenAPI/report contracts and R09 remain pending. No migrations, real database tests, production readiness or clinic sign-off is established.
