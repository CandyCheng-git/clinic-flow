# Business Processes

Version: 0.4 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Current-state clarification — 27 November 2026

Source: Candy Cheng's project clarification on 27 November 2026. These are user-reported operational facts, not an independently observed end-to-end workflow or owner-approved policy.

| Topic | Reported fact | Still unconfirmed |
| --- | --- | --- |
| Customer identification | Paper customer files carry a customer ID | ID format, uniqueness, leading zeros and how IDs are assigned |
| Existing software | The clinic has a POS system described as approximately ten years old | Product, version, supported functions, export/API capability and support status |
| Transaction recording | Staff use handwritten transaction slips, then manually enter the information into the existing system | Exact fields, entry timing, responsible role and review process |
| Cross-record matching | Paper files and an existing system coexist | Whether the POS stores the same customer ID, a different ID, or another reference |
| Voucher records | Booking, package purchase and remaining-session records are in project scope | Which are stored on paper/in POS, when balances change and which record takes precedence |

The confirmed sequence is **handwritten slip → manual entry into the existing POS**. The full journey from booking through service delivery, purchase, redemption and reconciliation has not yet been established. Re-entry occurs; its duration and error rate have not been measured. No specific duplicate deduction, lost slip or balance discrepancy is established by this clarification.

## AS-IS follow-up

1. Trace one synthetic example from a paper customer file to a handwritten slip and its POS entry. Record the identifiers and fields used at each step.
2. Establish whether the paper customer ID also appears in the POS; do not assume a shared key or match solely on name/phone.
3. Establish when entry happens, who performs it, how pending/already-entered slips are distinguished and who checks the result.
4. Establish where purchase and remaining-session figures are kept, when deductions occur and which record is authoritative if records disagree.
5. Check export capability before planning migration or integration. An approximate age alone does not establish that an API/export exists or is absent.

## Proposed transition boundary

Use a separate ClinicFlow internal customer ID. Evaluate preserving paper and POS identifiers as source-labelled external references after the mapping is confirmed; do not silently replace or merge existing IDs. A parallel-run or replacement plan needs an agreed source of truth, cut-off time and reconciliation process. POS integration, automatic synchronization, OCR, historical import and opening balances are not added to the demo scope by this clarification.


## TO-BE: user-clarified operational loop

Source: Candy, 2026-11-27; DEC-20261127-02–05. These requirements do not verify legacy POS behavior.

~~~mermaid
flowchart TD
    A[Locate or create customer] --> B[Schedule and complete appointment]
    A --> C[Record package purchase]
    C --> D[Select purchased voucher and review usage]
    B --> D
    D --> E[Customer signs paper]
    E --> F[Staff confirms paper signature in system]
    F --> G[Automatically attempt one deduction]
    G -->|Committed| H[Show balance and immutable history]
    G -->|Confirmed failure| I[Retain evidence and mark for review]
    G -->|Response lost| J[Check original operation outcome]
    I --> K[Staff rechecks saved work]
    J --> K
    K --> G
    H --> L[If mistaken create correction ticket]
    L --> M[Owner or Reception executes linked reversal]
~~~

Purchase and booking can occur in either order. Completion and staff-confirmed paper signature must both exist before automatic deduction; no separate manual redemption click. Signing summary binds customer, booking, service, voucher and one session. Changes require fresh reviewed paper confirmation, retaining old evidence.

## Draft and recovery

Either role saves incomplete input as Draft without purchase/completion/signature/deduction posting. Already committed completion/paper confirmation survives a failed deduction, which leaves no partial ledger/balance/audit/receipt. Show ReviewRequired with reason. A crash before posting leaves Pending. Lost response shows OutcomeUnknown until original-operation lookup/retry resolves it; never blindly deduct again. See [SRS](../02-system-requirements/SRS.md).

## Voucher purchase

Review customer/package/price/session/service snapshots and confirm. Voucher, opening ledger, balance, audit and receipt commit together. This records a purchase, not a payment or proof of cash receipt.

## Automatic deduction

Staff confirms customer paper signature using a synthetic paper reference in demo; record actor/time and frozen usage summary. Once completion exists, validate ownership, eligibility, access and balance, then post one session atomically. Retries retain the same intent; booking active-pointer/operation protection and voucher concurrency control prevent duplicate/negative balances. No signature image upload.

## Ticket-linked correction

Owner and Reception both create tickets with original Redemption/reason and execute reversal without extra approval. Open has no balance effect. Success appends +1 linked to original and updates balance/ticket/audit/receipt atomically. Failure leaves Open with no partial correction. Double reversal is rejected across tickets. Preserve mistake, paper evidence and ticket. Revised DA-07 permits another correct voucher after committed reversal. Either role selects eligible same-customer voucher, confirms corrected paper summary and the system attempts a new linked deduction. Failure keeps prior restoration committed and shows pending/review; retries never reactivate old deduction. Booking remains Completed, with at most one active deduction.

## Cancellation and no-show

Record status/reason; demo does not auto-deduct. Penalties, refunds, transfers, expiry and fractional usage remain unconfirmed. POS timing, authoritative records and ID mapping remain open despite the specified ClinicFlow trigger.
