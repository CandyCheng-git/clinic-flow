# Development Roadmap — Local Core and Hybrid Demo

Version: 0.5 | Updated: 27 November 2026

Status: Planning draft — 0/16 milestones verified complete; implementation not started

Source: user-supplied hybrid-demo proposal, received 27 November 2026 (DEC-20261127-07). Preserve DEC-20261127-02–06: staff-confirmed paper signing, automatic deduction, both-role ticket reversal and correct-voucher replacement. Hybrid presentation is preferred after real core delivery; local/video remains a valid fallback.

These 16 milestones group the [44 core tasks](project-scope.md) plus conditional mock work; they are not 16 additional core tasks or a completed-work percentage. Completion needs actual evidence. M09–M12 may be recorded Deferred if the optional public mode is omitted; existing core gates still apply. Dates/effort need estimation after a working first slice.

## Phase 1 — Core system (0/4)

- [ ] M01 Review BRD, SRS, MVP scope and assumptions; resolve implementation blockers (F01–F06, R01–R03).
- [ ] M02 Build .NET API/PostgreSQL, migrations and synthetic seed with contract/transaction design review (R04–R07, R09, D01–D02).
- [ ] M03 Build shared React customer/purchase/booking/paper-confirmation/deduction/ticket-replacement/history screens (R08, D04–D10).
- [ ] M04 Reproduce local setup with Docker Compose and full synthetic workflow from a clean checkout (O01, O03).

Identity and testing accompany these slices; Phase 2 records completion/evidence, not permission to defer all checks until UI completion.

## Phase 2 — Quality and security (0/4)

- [ ] M05 Verify real OAuth2/OIDC, active staff and role/resource policies, including both-role correction rights (D03, Q05).
- [ ] M06 Prove PostgreSQL transactions/rollback, retries, final-session concurrency and one-active-deduction correction chains (Q01–Q04).
- [ ] M07 Implement and exercise API rate/request/query limits, bounded pagination, safe logs and secrets handling (Q05, O05).
- [ ] M08 Run tests and GitHub Actions, benchmark real reads and rehearse backup/restore with retained results/defects (Q06–Q07, O02, O04).

## Phase 3 — Public interactive simulation, conditional (0/4)

- [ ] M09 Add explicit mock/live builds, MSW simulation, shared DTOs and persistent simulated-mode/role labels; no live-service fallback (FR-D01–D03, TC-040–042, TC-045).
- [ ] M10 Add versioned IndexedDB seed/persistence/isolation and coherent Reset Demo with stale-tab/request protection (FR-D04–D05, TC-043–044).
- [ ] M11 Review current Cloudflare Pages plan/settings and release static-only artifact if budget/checks fit; retain commit/build/network/smoke evidence (FR-D06, TC-041, TC-045).
- [ ] M12 Add tested public link, browser/reset instructions and simulation limits to README; do not invent a deployment URL (P01, P05–P06).

## Phase 4 — Portfolio evidence (0/4)

- [ ] M13 Record real full-stack workflow and representative integrity/correction test evidence (P04).
- [ ] M14 Update architecture/ADRs, case studies and screenshots to actual implementation (P01–P02, P05).
- [ ] M15 Record reproducible test/performance/recovery results and known limits with commit/environment/fixture (Q06, O04, P07).
- [ ] M16 Prepare repository release/demo docs and evidence-based CV/LinkedIn/interview wording; publish/send only when separately authorized (P03, P06).

Local repository/demo/video can satisfy Portfolio Release if Phase 3 is deferred. Roadmap checkboxes cannot substitute for core acceptance evidence.

## Phase 5 — Future cloud full stack, optional

Evaluate Render/Neon or another reviewed host after core/evidence. Azure Cloud/AWS are deferred; Azure DevOps is optional and not required for GitHub Actions. No resource, account, domain or subscription is created by this plan. Commercial use, import, privacy and support remain separate decisions.

## Immediate next slice

Synthetic customer -> purchase -> completed booking -> staff paper confirmation -> automatic deduction -> history -> ticket reversal -> another correct voucher. Prove retries/final-session/one-active-deduction behavior against PostgreSQL, then expand UI/features. Do not delay this slice for public mock polish.

See [demo strategy](../07-deployment/demo-strategy.md) for boundaries and official references and [change log](change-log.md) for decisions.
