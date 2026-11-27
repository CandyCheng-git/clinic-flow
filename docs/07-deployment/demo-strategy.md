# Hybrid Demo Strategy

Version: 0.5 | Updated: 27 November 2026

Status: Proposed delivery direction — not implemented or deployed

## Decision and source（展示方向）

DEC-20261127-07 records the user-supplied hybrid-demo proposal received on 27 November 2026. Preferred portfolio delivery is one shared React frontend with optional public simulation, plus the genuine local full-stack application and recording. Build core MVP and quality evidence first. Local demo/video remains a valid fallback if simulation work exceeds available time. Azure Cloud/AWS are deferred; Azure DevOps is optional; GitHub Actions remains planned.

目標額外支出 AUD $0。公開版展示操作流程；真正登入、SQL transaction、並發及權限由本機 API／PostgreSQL 測試提供證據。此文件不代表已部署、已開帳戶或已通過驗收。

## Two modes

| Concern | Public interactive simulation | Local full stack |
| --- | --- | --- |
| Frontend | Shared React/TypeScript screens and API DTO expectations | Same shared screens/contracts |
| Data | MSW browser handlers + IndexedDB synthetic state | ASP.NET Core API, EF Core + PostgreSQL |
| Identity | Explicitly simulated Owner/Manager and Reception role selector | Real selected OAuth2/OIDC provider and backend staff/RBAC checks |
| Label | Interactive Demo — Simulated Backend | Local Full Stack — Real API and PostgreSQL |
| Workflow | Synthetic customer, purchase, booking, paper confirmation, automatic deduction, ticket reversal, correct-voucher replacement, history/dashboard | Same use cases with actual transaction/concurrency/access enforcement |
| Persistence | Per browser profile/origin; same-origin tabs may share state | Local synthetic database |
| Reset | Only this demo's browser records; deterministic seed | Separate local reset/restore runbook |
| Evidence | UI and simulated behavior only | Actual retained API/database/security/performance results when executed |
| Hosting | Cloudflare Pages candidate, static artifact only | Local machine; no public tunnel/port forwarding required |

Public role switching is not authentication/security. Browser data is editable/clearable; it is not a clinic ledger, backup or proof of database integrity. Public simulation cannot satisfy real PostgreSQL rollback/concurrency, OIDC, authorization or API benchmark gates. No real customer data or signature images are used.

## Shared UI and build boundary

Proposed build selectors are VITE_API_MODE=mock and VITE_API_MODE=live. They are future configuration, not implemented commands. Keep a shared API client/screen tree and stable DTOs; MSW implements the supported demo subset. Real domain rules stay on the server. Reuse synthetic scenario expectations to detect contract drift without treating mocks as backend evidence.

Build separate public/live artifacts on distinct origins. Public mode is fixed in its artifact: visitor-controlled URLs or the role selector cannot switch to live. Frontend variables are public; contain no secrets. Unknown/missing mode fails explicitly. Live mode initializes no mock worker/handlers/storage or impersonation UI; failed login/API cannot fall back to mocks.

Public initialization awaits MSW and storage readiness before business requests. Missing handler, service-worker registration failure or denied/full storage shows a useful demo-unavailable/retry/reset state; it never falls through to localhost, cloud API or real identity. Normal static-asset traffic is allowed; no business writes, real login requests or unapproved telemetry leave the browser. Browser MSW is not a Cloudflare edge Worker. No Pages Functions, cloud database or paid edge service is required by this design.

## Browser persistence and reset

IndexedDB is the proposed primary mock business store; LocalStorage is limited to non-sensitive display preferences. Store customers, bookings, purchases, ledger, paper confirmations, drafts, operations and ticket/replacement chains coherently in one versioned synthetic dataset. Browser transactions simulate workflow consistency; they do not prove PostgreSQL guarantees.

Independent browser profiles/origins get independent seeds. Same-origin tabs share data: coordinate changes/reset notifications and invalidate stale views/writes. Do not promise independent data per tab. Surface quota, version-upgrade and cleared-storage failures instead of claiming persistence succeeded.

Reset requires confirmation, invalidates pending demo operations using a generation marker, restores the complete seed coherently and notifies other tabs. Discard prior-generation writes/responses so an old request cannot repopulate reset data. Clear only this application's demo records; never another site's storage or the local API/PostgreSQL. A new dataset version requires explicit migration/reset handling.

## Delivery and budget

1. Complete real local core, identity, integrity, API rate/input/query limits, tests and GitHub Actions. Docker Compose is the proposed launch mechanism; exact runnable setup awaits implementation.
2. Add optional simulation/persistence/reset and evidence labels. Include latest paper-confirmation, both-role ticket reversal and correct-voucher replacement rules.
3. Run conditional public checks, inspect built assets/network, and review host plan/settings before releasing static files. Add a URL only after deployment/smoke evidence exists.
4. Record real full-stack operation/tests and distinguish them from simulation in README/video/CV. Local/video delivery is sufficient if optional public mode is deferred.

AUD $0 is a spending target assuming existing hardware/internet and suitable software licenses. Cloudflare Pages is a candidate, not an account or cost guarantee; review current plan, limits and billing settings at release and use local fallback if unsuitable. No paid subscription, domain purchase or automatic upgrade is required. Render/Neon remain optional future full-stack candidates; current pricing/overage promises from the supplied text are not adopted without later verification.

Auth0 remains an identity candidate; evaluate a self-hosted option such as Keycloak if fully local identity is needed. No provider switch/setup is recorded. Azure learning may remain separate; do not claim Azure deployment experience from a design.

## Acceptance and evidence

FR-D01–D06, AC-DM and TC-040–TC-045 apply only if public mode is selected for release. They do not block core local delivery. See [scope](../00-project-governance/project-scope.md), [roadmap](../00-project-governance/development-roadmap.md) and [test report](../06-testing/test-report.md). All application checks remain Not run.

## Official references checked 27 November 2026

Cloudflare documents static requests that do not invoke Functions as free/unlimited, with Functions accounted separately; other project/account limits still apply. See [Pages pricing](https://developers.cloudflare.com/pages/functions/pricing/) and [platform limits](https://developers.cloudflare.com/pages/platform/limits/). Recheck at release; this is not an SLA or permanent cost guarantee.

MSW documents browser interception via service worker and asynchronous registration. This project intentionally proposes mock initialization in the public simulated build only, isolated from genuine live mode. See [MSW browser integration](https://mswjs.io/guides/integrations/browser).
