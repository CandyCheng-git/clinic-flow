# Project Proposal and Master Blueprint

Version: 0.5 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Project overview

| Item | Proposal |
| --- | --- |
| Project | ClinicFlow |
| Owner | Candy Cheng |
| Type | Real-world full-stack portfolio project |
| Industry | Beauty and wellness |
| Approach | MVP first; iterative delivery |
| Spending target | AUD $0 additional expenditure |
| Data | Synthetic data using realistic business scenarios |
| Current target | Gate 5 — Portfolio Release |

ClinicFlow will consolidate appointments, voucher purchases and remaining treatment-session records. A central database and API will support consistent balances, role-based staff access, traceable corrections and basic reporting. A business pilot is a possible later stage, subject to authorization and readiness.

## Objectives

Business objectives are to reduce repeated entry, make customer and booking lookup easier, prevent inconsistent session balances, preserve transaction history and provide clearly defined operational summaries. These are intended benefits, not established outcomes.

Technical objectives are a complete full-stack workflow, backend authentication and authorization, database integrity, automated verification, containerized development, a reproducible delivery process and maintainable module boundaries.

Career objectives are demonstrable evidence of requirements analysis, system design, REST APIs, database design, OAuth/OIDC, software quality, testing, deployment and technical decision-making. Demonstrated understanding matters more than the number of tools listed.

## Milestones

| Milestone | Deliverable | Initial planning allowance |
| --- | --- | --- |
| M0 | Scope, assumptions and first-slice acceptance criteria | 2–3 working days |
| M1 | Architecture, migrations, identity skeleton and local setup | 3–5 working days |
| M2 | Customer and appointment workflow | About 1 working week |
| M3 | Voucher purchase, redemption, reversal and integrity checks | About 1 working week |
| M4 | Integrated frontend and basic dashboard | About 1 working week |
| M5 | Critical tests, security checks and demo delivery | About 1 working week |
| M6 | Evidence, walkthrough and portfolio presentation | 2–3 working days |

The source's 4–6 week estimate is an unvalidated planning hypothesis. The listed allowances total roughly 5–6.2 five-day working weeks if sequential, before contingency. Re-estimate after the first working slice and record actual available hours. Documentation, testing and UI work should accompany each slice; these are not isolated waterfall stages.

## Budget

| Resource | Additional spending target | Basis |
| --- | --- | --- |
| Development, database and testing tools | AUD $0 | Local tools, subject to license terms |
| Version control and CI | AUD $0 | Local Git; external quotas to verify |
| Authentication | AUD $0 | Candidate provider terms to verify |
| Demo hosting | AUD $0 | Optional; use local demo/video if unsuitable |
| Documentation | AUD $0 | Markdown and Mermaid |
| Total | AUD $0 | Target, not a guarantee |

Production hosting, backup, security operations, maintenance and support are outside this budget. Do not enable paid resources to meet the demo goal without a separate budget decision.

## Success criteria

Technical success requires a complete core workflow, non-negative balances, duplicate-request protection, enforced permissions, passing critical tests, a working pipeline, verified backup recovery and documented performance measurements. All remain unverified.

Business improvement can be evaluated only after an authorized trial. Capture baseline lookup time, booking handling time, balance discrepancy frequency, repeated entry and staff feedback before any before/after claim.

Portfolio success requires a runnable application, repository, architecture notes, test evidence, reproducible benchmark, short demo, evidence-backed case studies and interview explanations.

## Release gates

| Gate | Exit criteria | Current status |
| --- | --- | --- |
| 1 — Requirements Ready | Core scope and demo assumptions reviewed; unresolved decisions identified | Not passed |
| 2 — Foundation Ready | Local environment, database and basic authentication work | Not passed |
| 3 — MVP Feature Complete | Core workflow implemented and demonstrable | Not passed |
| 4 — Quality Ready | Critical tests and relevant security checks pass | Not passed |
| 5 — Portfolio Release | Demo, documentation and evidence available | Not passed |
| 6 — Business Pilot | Business authorization, synthetic-data UAT arrangements and responsibilities agreed | Outside current commitment |
| 7 — Production Release | Operational, privacy, security, recovery and cost requirements assessed | Outside current commitment |

Gate 1 can use documented demo assumptions for portfolio development. A stakeholder's silence does not confirm clinic policy.

## Deliverable register

| ID | Deliverable | Location or status |
| --- | --- | --- |
| D01 | Project proposal | This file |
| D02 | Business requirements | `../01-business-requirements/BRD.md` |
| D03 | Software requirements | `../02-system-requirements/SRS.md` |
| D04 | Business process diagrams | `../01-business-requirements/business-process.md` |
| D05 | Stories and acceptance criteria | `../02-system-requirements/` |
| D06 | High-level design | `../03-system-design/HLD.md` |
| D07 | Low-level design | `../03-system-design/LLD.md` |
| D08 | ERD | `../03-system-design/ERD.md` |
| D09 | Data dictionary | `../03-system-design/data-dictionary.md` |
| D10 | UI wireframes | `../04-ui-ux/wireframes.md` — text specifications only |
| D11 | OpenAPI specification | Planned; API design notes are not an executable specification |
| D12 | Architecture decisions | `../05-architecture-decisions/` — proposed |
| D13 | Source code | Planned; `src/` reserved |
| D14 | Test plan and report | `../06-testing/` — no executed results |
| D15 | Security assessment | Planned; security design is not an assessment |
| D16 | CI/CD pipeline | Planned; workflow directory reserved |
| D17 | Deployment guide | `../07-deployment/deployment-guide.md` — proposed runbook |
| D18 | User guide | Planned after UI implementation |
| D19 | Risk register | `risk-register.md` |
| D20 | Benchmark report | `../06-testing/performance-report.md` — not run |
| D21 | Demo video | Planned |
| D22 | Technical case studies | `../08-portfolio/` — evidence templates |

## Preferred demo direction — DEC-20261127-07

The user-supplied hybrid proposal adds an optional public MSW/IndexedDB simulation using shared React screens, after genuine local core/quality delivery. Cloudflare Pages is a static-host candidate; current plan/settings require release-time review. Target AUD $0 additional spend, with local/video fallback. Azure Cloud/AWS deferred, Azure DevOps optional, GitHub Actions retained. Public mode is explicitly simulated and cannot count as real backend security/integrity/performance evidence.

See [roadmap](development-roadmap.md) for 16 grouped milestones and [strategy](../07-deployment/demo-strategy.md) for conditions. The 44 core tasks and all unpassed gates remain; Phase 3 public work may be deferred without changing core acceptance. Milestone allowances above exclude the new mock scope and need re-estimation rather than assuming it fits the original schedule. No public URL/resource/account has been created.
