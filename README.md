# ClinicFlow — Beauty Clinic Management System

Version: 0.8 | Updated: 27 November 2026

Status: Documentation baseline — implementation not started

ClinicFlow is a proposed staff-facing application that brings customer records, appointments, voucher purchases and remaining treatment sessions into one system. It is based on a real beauty-clinic workflow and will use synthetic data for development and portfolio demonstrations.

**Current delivery: an English documentation starter pack. No application, deployment, test execution or measured business improvement is included.** The project name, technology choices and clinic policies remain proposals unless explicitly validated.

## Latest business clarification

Candy reported on 27 November 2026 that paper customer files carry customer IDs, the clinic uses an approximately ten-year-old POS, and handwritten transaction slips are subsequently entered manually into the existing system. Paper/POS ID equivalence, the authoritative balance record and entry/reconciliation responsibilities remain unconfirmed. See [business processes](docs/01-business-requirements/business-process.md) and the [business requirements](docs/01-business-requirements/BRD.md).

Candy further specified the TO-BE workflow: completed booking plus staff confirmation of customer paper signature automatically attempts one deduction; failed/unknown posting needs safe draft/recheck handling; mistaken deductions require a correction ticket, and both Owner and Reception may execute reversal without extra approval. See [SRS v0.5](docs/02-system-requirements/SRS.md). This supersedes manual-redemption DA-06 and Manager-only DA-08. Candy also confirmed replacement using another correct voucher after ticket reversal (DEC-20261127-06), replacing DA-07’s permanent one-deduction limit with at most one active deduction and linked correction history. Paper/POS mapping and actual legacy timing remain open.

No POS integration or historical import is added. Files changed for the hybrid-demo planning revision carry v0.5; other files retain their recorded versions.

## Delivery direction — local core first, optional public simulation

The supplied hybrid proposal sets a preferred presentation direction (DEC-20261127-07): shared React UI, genuine local ASP.NET Core/PostgreSQL/OIDC mode, and optional public MSW/IndexedDB simulation on a reviewed static host. Public screens must say **Interactive Demo — Simulated Backend**, use synthetic browser-local data and offer Reset Demo. Public mode has no real API/database/login connection. Azure Cloud/AWS are deferred; GitHub Actions remains planned. No application site, CI pipeline or cloud hosting resource has been configured.

Build core and real checks first. See the [16-milestone roadmap](docs/00-project-governance/development-roadmap.md) and [demo strategy](docs/07-deployment/demo-strategy.md). Hybrid is preferred, not a new core-MVP release gate; local demo/video remains a fallback.

## Start here

1. Read the [project proposal](docs/00-project-governance/project-proposal.md) and [MVP scope](docs/00-project-governance/project-scope.md).
2. Review the [business rules and open questions](docs/01-business-requirements/business-rules.md). Separate clinic decisions from demo assumptions.
3. Implement one vertical slice: synthetic customer → package purchase → completed booking → staff paper-signature confirmation → automatic one-session deduction → history and ticket correction.
4. Prove duplicate-request and last-session concurrency behavior against PostgreSQL before expanding the UI.
5. Complete the remaining appointment, identity, reporting and release work incrementally. Update evidence as work is performed.

Do not spend weeks polishing these documents before building. Write enough to make the next slice testable, then revise the design using implementation evidence.

## Documentation index

| Area | Documents |
| --- | --- |
| 00 — Project governance | [Proposal](docs/00-project-governance/project-proposal.md), [Roadmap](docs/00-project-governance/development-roadmap.md), [Scope and checklist](docs/00-project-governance/project-scope.md), [Risks](docs/00-project-governance/risk-register.md), [Change log](docs/00-project-governance/change-log.md) |
| 01 — Business requirements | [BRD](docs/01-business-requirements/BRD.md), [Processes](docs/01-business-requirements/business-process.md), [Rules and questions](docs/01-business-requirements/business-rules.md), [Stakeholders](docs/01-business-requirements/stakeholders.md) |
| 02 — System requirements | [SRS](docs/02-system-requirements/SRS.md), [User stories](docs/02-system-requirements/user-stories.md), [Acceptance criteria](docs/02-system-requirements/acceptance-criteria.md), [Traceability](docs/02-system-requirements/traceability-matrix.md) |
| 03 — System design | [HLD](docs/03-system-design/HLD.md), [LLD](docs/03-system-design/LLD.md), [ERD](docs/03-system-design/ERD.md), [Data dictionary](docs/03-system-design/data-dictionary.md), [API](docs/03-system-design/api-design.md), [Security](docs/03-system-design/security-design.md) |
| 04 — UI and UX | [Wireframe specifications](docs/04-ui-ux/wireframes.md), [User journeys](docs/04-ui-ux/user-journeys.md) |
| 05 — Architecture decisions | [ADR-001: Modular monolith](docs/05-architecture-decisions/ADR-001-modular-monolith.md), [ADR-002: Database selection](docs/05-architecture-decisions/ADR-002-database-selection.md) |
| 06 — Testing | [Test plan](docs/06-testing/test-plan.md), [Test cases](docs/06-testing/test-cases.md), [Test report](docs/06-testing/test-report.md), [Performance report](docs/06-testing/performance-report.md) |
| 07 — Deployment | [Deployment guide](docs/07-deployment/deployment-guide.md), [Demo strategy](docs/07-deployment/demo-strategy.md), [Backup and restore](docs/07-deployment/backup-restore.md), [Monitoring](docs/07-deployment/monitoring.md) |
| 08 — Portfolio | [Technical case studies](docs/08-portfolio/technical-case-studies.md), [Interview STAR drafts](docs/08-portfolio/interview-star.md), [Lessons learned](docs/08-portfolio/lessons-learned.md) |

The folder names and filenames follow the suggested GitHub documentation structure. `src/`, `tests/`, `scripts/` and `.github/workflows/` are reserved with `.gitkeep` files. They contain no implementation or active automation.

## Proposed stack

React + TypeScript + Vite + Tailwind CSS; C# / ASP.NET Core 10; EF Core; PostgreSQL; OpenAPI; OAuth 2.0 / OIDC with PKCE; Docker Compose; GitHub Actions. See the [SRS](docs/02-system-requirements/SRS.md) for tools and unresolved choices. Optional public mode adds MSW/IndexedDB and Cloudflare Pages as static-host candidate. Pin compatible versions when implementation starts.

## Evidence and status conventions

| Label | Meaning |
| --- | --- |
| Known context | Described in the source blueprint; not independently observed by this project |
| Proposed | Suggested requirement, design or engineering target |
| Pending validation | Requires a stakeholder answer or technical verification |
| Demo assumption | Explicit provisional behavior for synthetic-data demonstrations |
| Implemented | Code exists and its commit is identified |
| Verified | A named check ran with retained evidence |

Requirements distinguish user-clarified TO-BE decisions from proposed engineering/demo details; see the decision register. All application test results are **Not run**. Architecture decisions are **Proposed**. Readiness gates have not been passed. Drafting documentation does not establish stakeholder approval, a working system or a measured result.

## Budget and operation

The target is **AUD $0 additional spending**, assuming an existing computer and internet connection. Local development and a recorded local demo are the baseline. External identity and hosting options require a current terms, quota and billing review before use. This pack does not claim that any cloud provider guarantees a free deployment.

No clinic records, internal documents, branding, passwords, tokens or API keys belong in this public portfolio. Commercial use, ownership, support and production readiness require a separate decision.

## Using this pack in GitHub

This directory is the repository root for [CandyCheng-git/clinic-flow](https://github.com/CandyCheng-git/clinic-flow). The committed baseline contains English project documentation and reserved source/test/workflow directories; application implementation has not started. Local-only reading copies, credentials and private data are excluded by `.gitignore`. No software license is selected: confirm ownership and intended reuse before adding one.

The original blueprint has been reorganized across these documents rather than retained as a second master copy. Keep the split documents authoritative; record material changes in the [change log](docs/00-project-governance/change-log.md).
