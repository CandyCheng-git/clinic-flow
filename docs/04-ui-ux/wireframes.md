# UI Wireframe Specifications

Version: 0.5 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

These are text specifications for future wireframes. No rendered mockups or working screens are included.

## Screens

| Screen | Essential content | Primary action | Important states |
| --- | --- | --- | --- |
| Login | Project identity, staff sign-in, synthetic-demo label | Sign in | Pending redirect, login failure, disabled staff |
| Dashboard | Clinic date, appointment counts, unredeemed completed appointments | Open calendar | Loading, no bookings, failure |
| Calendar | Day/week switch, staff filter, time-zone label, appointment states | Create booking | Conflict, stale edit, empty date |
| Customer list | Search, paginated results, archived filter | Create customer | No match, invalid search, error |
| Customer profile | Basic details, bookings, purchased vouchers, remaining sessions | Record purchase / open appointment | Archived profile, zero balance |
| Package administration | Package catalog, price, sessions, eligibility | Save definition | Manager-only, invalid quantity |
| Purchase form | Customer, package, original price, sessions and eligible services | Confirm purchase | Validation, pending, same-action retry |
| Paper signature / automatic deduction | Customer, booking, service, selected voucher, usage summary, paper reference, balance/outcome | Confirm customer paper signature / save draft | AwaitingSignature, Pending, ReviewRequired, OutcomeUnknown, Redeemed |
| Transaction history / correction ticket | Original mistake, actor/time, ticket reason/state and reversal link | Either role creates ticket and executes reversal | Open, reversal failure, Reversed, AwaitingReplacement, replacement failure, Replaced, already reversed |
| Staff administration | Schedulable staff and active account access | Save / disable | Manager-only, stale changes |
| Reports | Defined metrics, date filters and data-as-of timestamp | Export CSV | No data, unauthorized, failure |

## Paper-confirmation and correction specification

Before the customer signs paper, show synthetic customer name/ID, booking, service, selected voucher and one-session consequence. Staff confirms paper signature with reference; once completed, this triggers automatic deduction without another normal-redemption button. No digital handwriting or signature-image upload.

Show server-returned balance/transaction only after posting commit. Failed posting retains saved evidence and safe reason for recheck; staff can reopen Draft. A lost response shows checking status and queries original operation rather than silently retrying a fresh deduction. Recheck uses same unchanged signed intent; changing signed details requires fresh paper confirmation. Disable duplicate clicks for feedback; backend enforces correctness.

Both roles can create a correction ticket and execute its linked reversal. Show original mistake, reason, status and reversal link; no Owner-approval screen. No balance changes on Open. Failure retains Open with recheck; successful +1/ticket state is atomic. After committed reversal, show Select correct voucher / Confirm corrected paper usage. Automatically attempt linked replacement after valid confirmation; no extra Owner approval. Show original/reversal/new links, restored old-voucher balance, new-voucher outcome and pending/review until success. Booking remains Completed; old-operation retries cannot re-deduct.

## Interaction standards

Desktop-first with responsive layouts. Provide visible labels, keyboard access, focus handling, text descriptions of statuses and readable contrast. Show loading, empty, success, validation and server-error states. Require explicit staff confirmation of paper signature before automatic deduction, and explicit ticket reversal action with reason. Do not use color alone to convey state.

Public demo screens must make synthetic data clear. Keep implementation configuration, tokens, raw exceptions and database details out of operational staff flows.

Review these specifications through the [user journeys](user-journeys.md) before producing polished designs.

## Conditional public simulation UI

Show Interactive Demo — Simulated Backend on every public screen, with explicitly simulated Owner/Reception selector and Reset Demo confirmation. Use the shared customer/booking/purchase/paper-confirmation/ticket-replacement screens with synthetic references only. Surface mock unavailable, storage failure and pending/reset states truthfully; no real login or API fallback. Reset clears coherent related records and refreshes other shared tabs. Live screens do not expose mock role/reset controls. See FR-D01–D06 and AC-DM; these are optional presentation requirements.
