# User Journeys

Version: 0.4 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Journey 1 — Locate a customer and schedule

Receptionist signs in, searches for the customer, opens or creates a profile, selects staff/service/time and confirms a booking. The API checks resource validity and scheduling overlap. On conflict, show a useful message and let the user choose another slot. The calendar displays the committed appointment in Melbourne time.

## Journey 2 — Purchase a package

Receptionist opens the customer profile, selects an active package, reviews price, sessions and eligibility, then confirms. The system records the purchase and opening ledger entry atomically. A lost response can be retried using the original operation key. The profile displays the recorded purchase and balance, without claiming an online payment occurred.

## Journey 3 — Complete and redeem

Owner or Reception completes treatment, selects a matching voucher and reviews customer/service/voucher/one-session usage before the customer signs paper. Staff confirms the paper signature with reference; when completion and confirmation both exist, the system automatically attempts one deduction. Show AwaitingSignature, Pending, ReviewRequired or Redeemed separately from booking completion. Saved evidence survives confirmed posting failure; incomplete work can be saved as Draft. Lost responses show checking status and resolve the original operation before retry. Changed signed details require fresh paper confirmation; no signature image upload or separate normal-redemption click.

## Journey 4 — Correct a mistaken deduction

Owner or Reception opens history, creates a ticket for the mistaken Redemption with a reason, then executes ticket reversal without extra approval. A linked positive entry restores the original once; original mistake, paper evidence, ticket and reversal remain visible. Failed reversal leaves ticket Open and no partial correction. After committed reversal, either role may choose another correct eligible voucher for the same customer and confirm corrected paper usage, automatically posting a linked replacement. Keep booking Completed. Show restored original voucher and new deduction separately; failure leaves replacement pending/review. Preserve full chain, at most one active deduction and original-operation replay protection.

## Journey 5 — Review operations

Receptionist views appointment counts and completed-but-unredeemed appointments. Manager additionally views recorded package sales and current outstanding sessions, filters applicable date-based measures and exports authorized records. Each metric states its meaning and time basis.

## Walkthrough review checklist

- Can staff identify the next action without learning technical concepts?
- Are customer, booking and voucher relationships visible before deduction?
- Can staff distinguish completed treatment, pending paper confirmation, failed/uncertain posting and committed deduction?
- Is a failed or uncertain request distinguishable from committed success?
- Do archived, disabled, zero-balance and forbidden states explain what happened?
- Can core tasks be completed with keyboard navigation?

Record participant, synthetic fixture, date, observations and follow-up issues when this walkthrough actually runs. No clinic participation is confirmed.
