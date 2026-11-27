# Documentation Change Log and Change Control

Version: 0.8 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Version history

| Version | Date | Change | Validation status |
| --- | --- | --- | --- |
| 0.8 | 2026-11-27 | Prepared English documentation baseline for CandyCheng-git/clinic-flow; retained existing remote history and credential/private-data ignore rules, added local-only Chinese exclusions, corrected repository publication instructions | Documentation publication only; 3/44 design reviews and 50 Not run application cases unchanged; no application implementation/deployment claimed |
| 0.7 | 2026-11-27 | Reviewed core ERD/API relationships and integrity; mapped signing preparation/version/current pointers, operation-scoped recheck, explicit evidence/posting receipts, ledger null/type/FK constraints and snapshot guards; added AC-DB/TC-046–050; recorded R05/R06 design review complete | 3/44 design reviews complete; R09 partial/pending; 50 application cases all Not run; schema/migrations/provider/runtime/ADRs not validated |
| 0.6 | 2026-11-27 | Completed requested first-slice HLD document review; added module ownership/use-case coordination, explicit evidence/posting/reversal/replacement boundaries and recovery; clarified replay ordering in LLD; recorded only R04 complete (1/44 core tasks) | Document review and consistency checks only; ADRs Proposed, application unimplemented/tests Not run, release gates unpassed |
| 0.5 | 2026-11-27 | DEC-20261127-07 records supplied hybrid-demo direction: local real core first, optional public MSW/IndexedDB static mode, Cloudflare Pages candidate, Azure/AWS deferred; added strategy/16-milestone roadmap, FR-D01–D06/NFR-014/US-13/AC-DM/TC-040–045 and synchronized scope/design/deployment/evidence limits | Planning/review only; 45 application cases Not run; limited official Pages/MSW reference check, no account/resource/implementation/deployment |
| 0.4 | 2026-11-27 | DEC-20261127-06 allows another correct voucher after committed reversal; revised DA-07, added FR-V13 and TC-037–039, replaced global ledger booking uniqueness with locked active-pointer/intent/chain design; synchronized requirements/design/API/UI/portfolio notes; Chinese review v0.5 | User-specified TO-BE; correction-form and transaction details proposed; 39 application cases all Not run |
| 0.3 | 2026-11-27 | Recorded Candy’s completed-booking + staff-confirmed paper-signature auto-deduction, safe draft/recheck and both-role ticket reversal; expanded SRS examples/NFR conditions; synchronized BRD/rules, stories/AC/traceability, design/API/UI and 36 planned cases; Chinese review v0.4 | User-clarified TO-BE, not independent legacy observation/sign-off; engineering recovery/limits proposed; application cases all Not run |
| 0.2 | 2026-11-27 | Recorded Candy’s paper customer-ID, legacy POS and handwritten-slip/manual-entry clarification; updated README, BRD, processes, open questions, risks, SRS and identifier design note; added Traditional Chinese BRD review v0.3 | User-reported AS-IS facts; no independent observation, policy approval, implementation or executed tests |
| 0.1 | 2026-11-27 | Reorganized the supplied project blueprint into English Markdown using the suggested GitHub structure; added explicit design assumptions and evidence templates | Draft; no implementation or stakeholder sign-off |

## Material clarifications in v0.7

- Source: Candy requested core Database Design/ERD/API review of relationships, transaction model and data integrity on 2026-11-27.
- Corrected missing/optional diagram links and added persisted selected-voucher/prepared-ticket/usage-revision/current-confirmation fields plus operation actor and claim-state shape.
- Distinguished FK/unique/NOT NULL/row-check enforcement from aggregate invariants requiring coordinated locking, transactions and reconciliation; generic audit references are not polymorphic FKs.
- Signing preparation has no ledger effect. Accepted evidence/receipt commits separately from automatic posting; explicit operation-ID recheck cannot execute a newer different intent. Superseded unposted intent stays non-executable.
- Tightened purchase catalog/archive guards, receipt-claim ordering and nullable result-pointer insertion to support coherent snapshots and avoid impossible FK cycles.
- Added five planned core cases TC-046–050 (50 total; 41 critical including 4 conditional public cases). All remain Not run. Official PostgreSQL constraint/locking/isolation references support mechanism distinctions, not this unimplemented schema.
- R05/R06 document/protocol reviews complete, R04 preserved, R09 remains pending beyond first-slice API mapping. No code/migrations/database run, clinic sign-off, ADR acceptance or release gate completion is claimed.

## Material clarifications in v0.6

- Source: Candy requested a concise HLD review on 2026-11-27. Reviewed against SRS v0.5, scope, LLD/ERD/API/security, ADR-001/002 and hybrid strategy.
- Defined module data ownership and one application-controlled unit of work for cross-module posting; retained one API/database and optional mock boundaries.
- Made saved completion/paper evidence, automatic deduction, ticket reversal and correct-voucher replacement/failure recovery explicit; one active deduction and retained correction history remain invariant.
- Corrected LLD replay order so an already-posted/reversed intent resolves to historical receipt/current outcome before checking mutable balance/eligibility.
- Recorded R04 document review complete with evidence; remaining 43 tasks and all release gates still pending. This is no ADR acceptance, clinic sign-off or runtime verification. No application test count/result changed.

## Material clarifications in v0.5

- Source: user-supplied hybrid-demo text received 2026-11-27. Preferred presentation, not a mandatory expansion of core MVP. Original 44 tasks/gates remain; new 16 milestones group delivery and optional simulation work.
- Keep .NET/React/PostgreSQL/EF Core/OIDC/Docker/GitHub Actions/tests; add optional MSW and IndexedDB. Cloudflare Pages candidate for static files; Azure Cloud/AWS deferred; Azure DevOps optional, Render/Neon future options.
- Build distinct public/live artifacts and origins. Public roles/storage/transactions are simulated, labels persistent, no live-service fallback. Coherent seed/reset invalidates stale requests/tabs.
- Preserve signature-triggered deduction, both-role ticket reversal, correct-voucher replacement and real backend proof. Conditional public critical cases do not replace core tests or block local/video fallback.
- AUD $0 remains a spending target. Official Pages static/Functions distinction and platform limits plus MSW browser integration were reviewed; account eligibility/billing/deployment remain unverified. No permanent-free, provider SLA or Render/Neon overage guarantee is adopted.
- No code, account, subscription, push, hosted release, CV/LinkedIn publication or test pass is created/claimed by these documentation changes.

## Material clarifications in v0.4

- User confirmed replacement after reversal; cancel here means deduction reversal, not appointment cancellation.
- Both roles may reverse then confirm another eligible same-customer voucher for automatic replacement; no extra approval.
- Retain original/reversal/ticket/new chain, one active deduction, historical receipts and pending replacement after failure.
- DA-07’s old restriction is superseded. Global unique ledger BookingId is replaced by locked booking active pointer and unique operation/predecessor/ticket-successor constraints.
- Existing paper-summary binding remains proposed; corrected-paper wording/endorsement handling needs review. No hosting/technology changes are made by this correction request.
- Plans and documentation only; no implementation or application tests executed.

## Material clarifications in v0.3

- DEC-20261127-02–05 records source/date and both direct user answers: paper signing followed by staff confirmation; Owner and Reception both reverse without extra approval.
- Superseded DA-06 manual-redemption trigger and DA-08 Manager-only correction; retained IDs for traceability.
- Added FR-B08–B10 and FR-V11–V12, AC-S/AC-D/AC-T, US-12 and TC-029–TC-036.
- Added proposed draft/evidence/operation/ticket model, recoverable transaction boundaries and measurable read workload.
- Retained DA-07 no-re-deduction limitation, POS mapping/timing questions and other demo policies; no POS integration, real signature-image storage or external ticket service.
- No implementation, application test pass, clinic sign-off or delivery gate completion is claimed.

## Material clarifications in original baseline

- Preserved the proposed C#/.NET, React and PostgreSQL direction; no framework switch was made.
- Kept uncertain clinic policies and provider costs unconfirmed.
- Made the ledger/concurrency, retry, correction and appointment-linkage proposals concrete enough to review.
- Added a purchase-service snapshot and idempotency record to the conceptual model; these are proposed implementation details beyond the original entity list.
- Scoped generic balance adjustments, refunds, transfers and expiry extensions outside this MVP.
- Replaced the unsubstantiated progress percentage with 44 unchecked verification tasks.
- Made performance reports, case studies and STAR answers explicitly unexecuted templates.
- Identified the mismatch between a 4–6 week headline and the source milestone allowances.

## Change request template

| Field | Value |
| --- | --- |
| Change ID | CR-TBD |
| Request date / requester | Pending |
| Business reason | Pending |
| Requirement / rule IDs | Pending |
| Priority and proposed release | Pending |
| Scope, API, schema, security and test impact | Pending |
| Effort and schedule impact | Pending |
| Decision / decision owner / date | Pending |
| Implementation commit and verification | Not implemented / not verified |

Use `Proposed`, `Accepted`, `Deferred` or `Rejected` for a recorded decision. Identify who accepted it and in what capacity; a developer's demo decision is not business policy approval.
