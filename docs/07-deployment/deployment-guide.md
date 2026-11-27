# Deployment Guide

Version: 0.5 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Runbook status

Proposed sequence only. There are no project files, dependency manifests, migrations, containers or workflows to run yet. Exact commands must be added and verified when those artifacts exist. Do not present placeholder commands as a working setup.

## Environments

| Environment | Intended purpose | MVP requirement |
| --- | --- | --- |
| Local | Daily development and synthetic demo | Required |
| Test | Isolated automated checks | Required |
| Local demo | Real API/PostgreSQL/OIDC walkthrough and recording | Required demonstration; cloud hosting optional |
| Public simulation | Optional static UI, MSW and browser-local synthetic data | Conditional; labelled simulated; no real business API/identity |
| UAT | Authorized business feedback | Later decision |
| Production | Live commercial operation | Outside current commitment |

## Local setup sequence

1. Pin compatible .NET, Node, EF Core/provider and PostgreSQL versions; record prerequisites.
2. Create API/frontend project manifests and the Compose definition; keep secrets outside Git.
3. Document server configuration for database connection, OIDC authority/audience, allowed frontend origin and clinic time zone.
4. Document public frontend configuration for API URL, OIDC issuer/client ID/audience and exact redirect URLs. Frontend build variables are public, so never place secrets in them.
5. Start an isolated local database, apply reviewed migrations and generate the deterministic synthetic fixture.
6. Start API and UI; verify health, login and the purchase/redemption workflow from a clean checkout.
7. Add the exact tested commands, expected URLs, troubleshooting notes and teardown/reset instructions here.

## CI/CD proposal

Pull request: restore/install from lockfiles, build, format/lint/static checks, relevant unit tests, PostgreSQL integration tests and dependency review. Build the container artifact after checks. Execute E2E checks in a prepared test environment when applicable. Security scans should have reviewed findings; a green badge alone is insufficient.

Demo deployment: choose a reviewed artifact, configure environment/secrets, take a required pre-migration backup, apply reviewed migrations, start the application, run health and smoke checks, and retain release/commit and logs. A local/video demo can satisfy portfolio delivery without cloud deployment.

Use feature branches, reviewed/self-reviewed pull requests, meaningful commits, release tags and a changelog. Protect the main branch where supported. No workflow YAML or deployment has been configured in this package.

## External service review

Auth0, Render and Neon are candidates from the blueprint. Before creating resources, verify current eligibility, limits, required features, region, idle/cold-start behavior, data retention, backups and billing controls. No account-specific eligibility/billing/free-tier suitability has been validated here. The limited official Pages/MSW reference review is recorded separately in demo strategy; it is not a resource-creation or hosting-cost approval. Select a local fallback if zero-spending cannot be maintained.

## Rollback and release record

Record artifact/commit, configuration version, migrations, backup reference, smoke outcomes and known limitations. Rollback the application only if the schema remains compatible. For destructive migrations, rehearse backup restoration into a clean database before release; do not assume a generated down migration will recover data.

See [backup-restore.md](backup-restore.md) and [monitoring.md](monitoring.md). Real customer records and production credentials are excluded from this workflow.

## Optional public static release

Follow [demo strategy](demo-strategy.md) after core quality delivery. Build with explicit mock configuration and synthetic-only assets; await MSW/storage, block unhandled business calls and never fall through to localhost/cloud API/OIDC. Public and live artifacts/origins are distinct. Do not publish local API/database secrets or expose ports/tunnels. Frontend build variables are public.

Before optional static release, verify TC-040–045, hosting plan/settings/build/file limits, worker script availability, SPA deep links, persistence/reset, mode labels and network requests. Cloudflare Pages is a candidate; no Functions/Workers/database binding is required. Release commit/artifact/static configuration and actual smoke results must be retained. Public simulation rollback reverts static artifact and handles browser dataset versions; it does not run EF migrations or restore PostgreSQL. Existing database backup/migration instructions apply only to real full-stack deployment.

Exact future launch target is docker compose up --build, pending a verified Compose definition and prerequisites. This documentation does not claim that command currently runs. Pin/configure chosen identity provider; a fully local option can be evaluated separately. Core CI must exercise actual PostgreSQL/security tests; add separate mock/live build/network checks if public mode is selected. No hosted deployment, repository push or CI workflow is performed by this plan.
