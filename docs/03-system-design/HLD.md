# High-Level Design

Version: 0.7 | Updated: 27 November 2026

Status: First-slice document review completed — proposed design; implementation and runtime verification pending

## Scope and review outcome

The architecture supports the first synthetic-data vertical slice in [SRS v0.5](../02-system-requirements/SRS.md): customer -> purchase -> completed booking + staff-confirmed customer paper signature -> automatic deduction -> history -> correction ticket/reversal -> another correct voucher. Owner maps to Manager; Reception maps to Receptionist. Both can operate the core flow and execute ticket-linked reversal/replacement without extra approval.

Use a modular monolith: one ASP.NET Core API deployment, one PostgreSQL database and shared React/TypeScript UI. Logical module/layer boundaries do not require separate services, projects or a message broker. The optional public mock is a later presentation build; local real-mode delivery comes first. See [scope](../00-project-governance/project-scope.md) and [demo strategy](../07-deployment/demo-strategy.md).

## System and trust boundaries

~~~mermaid
flowchart TD
    S[Shared React screens and API DTOs] --> R[Fixed live build - local origin]
    S --> M[Optional fixed mock build - public origin]
    R -->|OIDC login with PKCE| I[Selected real identity provider]
    R -->|Access token and API requests| A[ASP.NET Core API]
    I -->|Token signing metadata| A
    A --> C[Application use-case coordinator]
    C --> P[Domain rules and EF Core transaction]
    P --> D[(Local PostgreSQL)]
    A --> L[Safe operational logs and health]
    M --> W[MSW browser handlers]
    W --> B[(IndexedDB synthetic state)]
~~~

Real mode validates access tokens, current active staff, role and booking/customer/voucher consistency at the API. Customers sign paper and have no application login. Persist staff confirmation/reference/time and the reviewed usage summary; no signature image capture. Never trust a client-supplied actor or displayed balance.

Mock mode is an independent static artifact/origin with persistent Interactive Demo — Simulated Backend labels and simulated roles. Await mock/storage readiness; failure has no real API/OIDC fallback. No connection from public mode to local API/PostgreSQL. Browser storage/reset is a simulation and supplies no real security, database concurrency or API-performance evidence.

## Module ownership and dependencies

| Module | Owns / writes | Cross-module use |
| --- | --- | --- |
| Customers | Profile, contact details, archive state | Supplies customer identity/status; name or shared phone does not establish identity |
| Appointments | Bookings, lifecycle, staff-slot coordination, paper confirmations, booking active-redemption pointer | Supplies completed booking/signed summary; pointer changes occur only within coordinated posting/reversal transactions |
| Staff | Staff identity mapping, role, active/schedulable state | Identity checks active staff; appointments use staff scheduling resources |
| Services | Service definitions, duration and active state | Appointments and package eligibility reference stable service IDs |
| Vouchers | Package definitions, purchased snapshots, balances, immutable ledger, deduction operations, correction tickets/replacement links | Uses booking/customer/service facts through coordinated use cases; never directly rewrites history |
| Identity / Access | Provider integration and backend access policies | Maps trusted issuer/subject to Staff; does not infer permission from token validity alone |
| Reporting | Read models/queries over committed records | Cannot mutate balances or bypass authorization; operational counts and Manager sales data remain distinct |
| Audit | Append-only business audit representation and correlation | Participates in the same transaction as each successful business mutation; failed attempts are separate safe operational logs |

API handles transport, authentication/policies, DTO validation and safe errors. Application coordinates use cases and a single database unit of work; domain rules define eligibility, quantities and transitions; infrastructure implements persistence/provider/logging. Signing preparation persists selected voucher/ticket context and a usage-summary version; it has no signature or ledger effect. Unposted evidence may be superseded, while its historical operation cannot post again. Draft storage is supporting workflow persistence with no authority to complete/sign/post. Do not duplicate a use case in controllers, UI or independently committing module repositories.

Cross-module writes to booking pointer, voucher, ticket, operation, audit and receipt share one application-controlled transaction when they constitute one posting/reversal. Database constraints and locking back the server rules; frontend disabling is only feedback. Detailed field/constraint/lock design remains in [ERD](ERD.md), [data dictionary](data-dictionary.md) and [LLD](LLD.md).

## Workflow and transaction boundaries

| Boundary | Committed together | Failure and recovery |
| --- | --- | --- |
| Purchase | Purchased price/session/service snapshots, voucher balance, opening ledger, audit and idempotent receipt | Failure leaves no partial purchase; retry same intent/key |
| Completion / paper confirmation | Completion is a saved lifecycle fact; paper confirmation saves its immutable summary and stable pending operation together | Neither prerequisite alone deducts. Once both exist, invoke posting automatically; saved evidence can survive later posting failure |
| Initial or replacement deduction | -1 ledger, selected voucher balance, booking active pointer, operation outcome, audit and receipt; replacement also records ticket successor | Posting failure rolls back these writes, retains earlier evidence and marks pending/review safely; recheck same operation |
| Ticket reversal | +1 original-voucher ledger/balance, cleared booking active pointer, original operation reversed, ticket reversal state, audit and receipt | Failure leaves ticket Open/no partial reversal; committed success allows a separate replacement intent |
| Replacement after reversal | New eligible same-customer voucher deduction, linked original/ticket/new operation and corrected paper confirmation | Earlier reversal stays committed if new posting fails; show AwaitingReplacement/ReviewRequired, not correction complete |

Completion and valid paper confirmation automatically trigger the deduction use case; no separate normal-redemption approval. A crash between evidence save and posting leaves discoverable Pending work. Initial demo recovery is a persisted operation plus staff recheck; no independent queue/worker service is required. Lost response is OutcomeUnknown until original-operation lookup resolves it; never assume rollback or create a fresh debit blindly.

A previously posted operation returns its historical receipt/current outcome before mutable eligibility/balance checks, including after reversal. Only an unposted intent goes through current posting checks. One successful intended operation has one effect regardless of key/actor retry. Preserve historical receipts separately from current workflow state and current balance.

## Integrity and correction invariants

- Balances never go negative and equal signed ledger totals. Snapshot changes in package catalog never rewrite purchased entitlements.
- Each booking has at most one active, unreversed deduction. Its locked active-redemption pointer and unique operation/predecessor references prevent duplicates across vouchers, actors and keys. Historical ledger BookingId is not globally unique because ticket correction can create a successor.
- A committed deduction can be reversed once through a reasoned internal ticket. A reversed deduction/ticket can produce one replacement using another correct eligible voucher for the same customer. Further correction gets a new ticket for that replacement. Preserve the full original -> reversal -> replacement chain.
- Reversal cancels the deduction, while the booking stays Completed. Old original/reversal requests never reactivate or refund a successor. Changed voucher details require reviewed corrected paper confirmation under the current proposed signature-binding design.
- Voucher balance races serialize on the voucher; booking-level races serialize on the booking. Appointment overlap prevention follows the shared staff scheduling protocol. Verify against independent real PostgreSQL connections; see TC-003–005, TC-008–009, TC-021–022 and TC-037–039 in [test cases](../06-testing/test-cases.md).
- Save only safe error/correlation details for failed attempts. A separately saved ReviewRequired state must not overwrite a concurrent committed success or newer current intent. Detect pointer/ledger mismatch for investigation rather than silently rewriting history.

## Deployment, decisions and next slice

Real local shape is React + API + PostgreSQL + the selected OIDC provider, with isolated test databases and deterministic synthetic seed. Docker Compose is a planned reproducible setup, not a command verified by this review. Optional static hosting remains a reviewed candidate; Azure Cloud/AWS deferred, no public tunnel required. No application deployment or resource creation has occurred.

| Decision | Current position / next check |
| --- | --- |
| Modular monolith / PostgreSQL | [ADR-001](../05-architecture-decisions/ADR-001-modular-monolith.md) and [ADR-002](../05-architecture-decisions/ADR-002-database-selection.md) remain Proposed; prove with runnable first slice |
| Runtime/provider versions and OIDC | Select/pin compatible versions and actual identity configuration during foundation setup; Auth0 candidate, self-hosted option still evaluable |
| Ledger/concurrency/idempotency | Proposed in LLD; implement constraints/lock order and run real database failure/race tests before claiming integrity |
| Paper form / legacy POS | Corrected-paper wording/reference/retention and paper/POS mapping remain open; explicit synthetic demo assumptions support first slice, not production cutover |
| Public simulation | Conditional after core/quality; own build/network/reset checks, no substitution for real backend evidence |

Next: check core ERD/API fields against these boundaries, then implement the first synthetic-data slice and its retry/final-session/correction tests. Other clinic policy gaps stay explicit; they do not become hidden implementation defaults. Core release gates remain unpassed.

## Review record — 27 November 2026

Review requested by Candy; performed as a document review against current SRS v0.5, scope, LLD/ERD/API/security, ADR-001/002 and demo strategy.

| Finding | Resolution / evidence |
| --- | --- |
| Module names lacked data ownership and cross-module transaction coordination | Added ownership table and one application-controlled transaction boundary |
| Core completion/signature/reversal/replacement and failure recovery were only summarized | Added explicit boundaries, one-active-deduction invariant and pending/unknown/replacement recovery |
| Footer referenced SRS v0.3 and omitted current correction context | Replaced with current SRS v0.5 references and DEC-20261127-06/07-aligned boundaries |
| LLD checked mutable balance/eligibility before recognizing an already-posted operation | Clarified replay-before-mutable-validation order; old reversed operations cannot post again |

Document review is complete; the above edits remove the identified HLD gaps for the first slice. Architecture decisions are still Proposed, runtime guarantees are untested, clinic policies are not signed off and no application-readiness gate is passed. R04 records only this document review. Link/reference and Markdown structure checks are recorded with the change; no application tests ran.

Core ERD/API review v0.7 adds persisted signing preparation/version/pointers, typed relationship/constraint mapping and operation-ID recovery. Evidence-save success is distinct from posting success; exact reviewed contracts are in API and data dictionary. R05/R06 record document review only; R09/full OpenAPI and runtime guarantees remain pending.
