# Business Rules, Demo Assumptions and Open Questions

Version: 0.4 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Business rule register

`Proposed` describes an engineering or workflow suggestion. `Pending validation` describes an unanswered clinic policy. Neither label establishes a clinic-approved rule.

| ID | Rule or decision | Status |
| --- | --- | --- |
| RULE-001 | Remaining sessions must never be negative | Proposed |
| RULE-002 | A voucher may be redeemed only for eligible services | Pending validation |
| RULE-003 | A staff member cannot hold overlapping appointments | Proposed |
| RULE-004 | Repeating one intended deduction must not deduct again | Proposed |
| RULE-005 | Posted transactions cannot be directly deleted | Proposed |
| RULE-006 | Correct through ticket-linked reversal then another correct voucher; preserve full correction chain | User clarified TO-BE, 2026-11-27 |
| RULE-007 | Owner and Reception can create tickets and execute reversals; no extra approval | User clarified TO-BE, 2026-11-27 |
| RULE-008 | Define voucher expiry and extensions | Pending validation |
| RULE-009 | Define deductions for cancellations | Pending validation |
| RULE-010 | Define deductions for no-shows | Pending validation |
| RULE-011 | Define transfer or sharing rights | Pending validation |
| RULE-012 | Define refunds and partial refunds | Pending validation |
| RULE-013 | Package price changes must not change historical purchase prices | Proposed |
| RULE-014 | Automatically attempt deduction after completion and staff confirmation of customer paper signature | User clarified TO-BE, 2026-11-27; legacy POS timing pending |
| RULE-015 | Define whether one redemption always consumes one full session | Pending validation |

## Explicit provisional demo assumptions

| ID | Assumption | Consequence |
| --- | --- | --- |
| DA-01 | One clinic; Manager and Receptionist roles | No multi-tenant permission model |
| DA-02 | Sessions are positive whole numbers; each redemption consumes one | No fractional or multi-session redemption UI |
| DA-03 | A package has an explicit service eligibility list, snapshotted on purchase | Catalog changes do not rewrite purchased eligibility |
| DA-04 | No automatic expiry in MVP; `ExpiresOn` is null | Expiry/extension rules must be designed before enabling them |
| DA-05 | Cancelled and no-show appointments do not deduct sessions | No automatic fee/deduction behavior |
| DA-06 | Superseded by RULE-014: completion plus staff-confirmed paper signature automatically triggers deduction | No separate manual redemption command; track saved evidence/posting separately |
| DA-07 | Revised by DEC-20261127-06: at most one active (un-reversed) deduction per booking; after committed reversal, another correct eligible voucher may be used | Preserve full correction chain; one replacement per reversed deduction/ticket; old-operation retries never re-deduct |
| DA-08 | Superseded in access/flow by RULE-006/007: either role reverses via ticket with reason | No extra approval; no direct balance edits or general adjustments |
| DA-09 | Transfers, sharing, refunds and imported balances are excluded | No cross-customer voucher use |
| DA-10 | Staff-only scheduling; adjacent appointments may touch at an endpoint | Intervals use start-inclusive/end-exclusive overlap logic |

These assumptions make a synthetic demo implementable while preserving unanswered business questions. They may change after validation; record any change and affected tests.

## Outstanding questions

| Area | Questions | Decision owner | Status |
| --- | --- | --- | --- |
| Current records | Paper customer files have IDs; handwritten slips are manually entered into an approximately ten-year-old POS (Candy, 2026-11-27). Which records/fields live in each source? How are pending slips tracked? | Clinic owner / receptionist | Partly answered; details pending |
| Customer ID mapping | Does POS use the paper ID? What are the format, uniqueness and duplicate-resolution rules? | Clinic owner / receptionist | Pending |
| Reconciliation | Who enters/checks slips, when does entry occur, where is voucher balance maintained and which record takes precedence? | Clinic owner / receptionist | Pending |
| Packages | What are the prices, quantities, eligibility, expiry and extension rules? | Clinic owner | Pending |
| Redemption | ClinicFlow trigger is completion + paper signature confirmed by staff. What is POS timing; can usage consume half/multiple sessions? | Clinic owner / therapist | TO-BE trigger user clarified; legacy timing/quantity policy pending |
| Exceptions | What happens on cancellation, no-show, refund, transfer or mistaken deduction? | Clinic owner | Pending |
| Scheduling | Are rooms, equipment, breaks or buffers also constrained? | Clinic manager | Pending |
| Access | Which staff may view customer data, prices, reports and corrections? | Clinic owner | Pending |
| Capacity | How many staff operate concurrently? What downtime is acceptable? | Clinic manager | Pending |
| Integration | Is historical import, offline support or a third-party connection needed? | Clinic manager / engineer | Pending |
| Participation | Will the clinic review requirements or join synthetic-data UAT? | Clinic owner | No agreement recorded |
| Ownership | Who owns code and clinic-specific material? Who may publish a portfolio? | Engineer / clinic owner | Pending |
| Operation | Who funds deployment and owns maintenance and support? | Clinic owner | Pending |

Record an answer, source, date and affected requirement IDs when a question is resolved. Do not infer consent from access to existing clinic systems.

## Decisions from Candy — 27 November 2026

| ID | Source / requested behavior | Status and affected IDs |
| --- | --- | --- |
| DEC-20261127-02 | User: Owner and Reception operate/input data; failed work needs recheck/draft handling | Requested TO-BE; recovery semantics proposed in FR-B09–B10 |
| DEC-20261127-03 | User: completed booking plus customer signature auto-deducts; retries do not double-deduct and concurrency cannot overdraw | Requested TO-BE; RULE-014, FR-B04/B08, FR-V05–V07; supersedes DA-06 |
| DEC-20261127-04 | User answer: customer signs paper, staff confirms in system | Selected method; paper reference/retention format pending; FR-B08, AC-S |
| DEC-20261127-05 | User answer: Owner and Reception both reverse; mistaken deduction requires ticket preserving mistake | Requested TO-BE; RULE-006/007, FR-V09/V11/V12, AC-T; supersedes Manager-only DA-08 |

These are user requirements, not independently verified legacy behavior or clinic-wide sign-off. Paper/POS IDs, record authority, actual POS timing and unanswered policies remain pending. DA-07 is revised by DEC-20261127-06 to allow another correct voucher after reversal, preserving at most one active deduction. Draft/review states, recovery, ticket states and NFR numbers are proposed design details rather than additional user-confirmed facts.

## Replacement after reversal — DEC-20261127-06

Source: Candy, 2026-11-27:「沖銷(cancel)後，可改扣另一張正確套票」。This specifies requested ClinicFlow behavior; it does not verify the legacy POS.

- Owner and Reception may both proceed after ticket reversal has committed. The booking stays Completed; cancelling a booking does not authorize replacement.
- Restore the mistaken voucher once; then use another eligible voucher of the same customer. Keep original deduction, reversal, ticket and new deduction linked. At most one un-reversed deduction for a booking at any instant.
- Preserve prior request receipts; never use replay of a reversed operation as replacement. Use a fresh correction intent linked to the reversed deduction/ticket. Retries of that new intent still affect balances once.
- Retain the existing proposed signature-summary binding: staff confirms corrected paper usage for the selected correct voucher; original confirmation remains history. Correction form wording/endorsement handling remains a design-review item, not an added approval gate.
- If replacement fails, the original reversal stays committed; mark pending/review and retry safely. Each reversed deduction/ticket can produce one replacement; correcting that replacement needs its own ticket/reversal.
