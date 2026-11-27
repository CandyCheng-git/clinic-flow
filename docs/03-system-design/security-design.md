# Security and Privacy Design

Version: 0.5 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Identity proposal — real API mode

Use an established OIDC provider and maintained SPA SDK, with Authorization Code Flow and PKCE. Auth0 is a candidate, not an activated or price-verified service. Register exact redirect/logout URIs and allowed origins. A public SPA cannot keep a client secret.

The API receives an access token and validates signature, expected issuer, audience and expiry with maintained middleware. An ID token describes authentication to the client and must not be used as an API access token. Token validation alone does not prove staff permission: map issuer/subject to a currently active Staff record and apply server-side policies and resource checks.

Use SDK-managed transient state for login correlation and OIDC nonce validation where applicable. Prefer in-memory access-token handling for the demo; choose and document renewal behavior explicitly. Do not introduce custom browser token storage or self-built token validation. Staff logout must clear the application session and token state; provider logout behavior must be tested.

## Proposed permission matrix

| Capability | Receptionist | Manager |
| --- | --- | --- |
| View/create/update/archive customer | Yes | Yes |
| View calendar and create/reschedule appointments | Yes | Yes |
| Change appointment lifecycle | Yes | Yes |
| Read minimal staff/service/package catalog | Yes | Yes |
| Record purchase; confirm paper signature; automatic deduction/recheck | Yes | Yes |
| Create/read ticket, reverse with reason, then confirm another correct voucher for replacement | Yes | Yes |
| Create/read/update own saved workflow draft | Yes | Yes |
| Read voucher balance and ledger | Yes | Yes |
| Administer services/packages/prices | No | Yes |
| View sales reports and report exports | No | Yes |
| Administer staff access | No | Yes |
| Read operational dashboard counts | Yes | Yes |

Single-clinic Receptionists can access that clinic's synthetic customers; the demo does not restrict each record to one Receptionist. It does enforce that a booking, customer and selected voucher match. Multi-tenant isolation is outside scope.

## Security controls to implement and verify

| Risk | Proposed control | Verification |
| --- | --- | --- |
| Injection | Parameterized EF/database access; no unsafe string SQL | Input and code review |
| Broken access control | Endpoint policies plus resource consistency checks | Direct API denial tests |
| XSS and token exposure | Safe rendering, bounded content, token redaction | UI/security review |
| Cross-origin exposure | Exact origin allow-list if CORS is needed | Disallowed-origin checks |
| Transport/secrets | HTTPS outside local exceptions; server secret configuration | Deployment review |
| Abuse | Bounded requests, pagination and appropriate rate limits | Negative and limit tests |
| Vulnerable dependencies | Pin versions and scan dependencies | CI scan evidence |
| Sensitive logs | No credentials, full tokens, request bodies or real customer records | Log inspection |
| Public demo misuse | Synthetic-only data, limited permissions, reset plan | Demo rehearsal |

If cookies/BFF architecture is introduced later, reassess CSRF and session handling rather than assuming bearer-token controls cover it. Disabled-staff access is checked on requests, not solely when a token is issued.

## Privacy and commercial boundary

Public source, seed data, screenshots and videos use synthetic data only. Minimize customer fields. Exclude medical records, photos and payment credentials. Do not use the clinic's name, brand or internal documents without authorization.

Applicable Australian/Victorian privacy, retention, consumer and incident obligations have not been assessed. This is an open production-readiness task, not a claim of legal compliance. A future health/medical-data scope requires a fresh assessment. Confirm ownership, authorized use and maintenance responsibilities before a business pilot.

## Official implementation references

- [Microsoft: JWT bearer authentication](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/configure-jwt-bearer-authentication?view=aspnetcore-10.0)
- [Auth0: Authorization Code Flow with PKCE](https://auth0.com/docs/get-started/authentication-and-authorization-flow/authorization-code-flow-with-pkce)

References support the protocol and middleware choices; they do not certify the proposed application's security.

Paper confirmation is an authenticated staff attestation of customer paper signing, not customer digital authentication or legal identity verification. Store confirming actor/time/reference and immutable reviewed summary; no signature-image collection in MVP. Both-role ticket reversal reflects Candy’s explicit 2026-11-27 answer; no extra Owner approval is imposed. Drafts are creator-only as a proposed demo policy. Safe operation/ticket outcomes remain single-clinic staff resources; no public lookup.

DEC-20261127-06 allows both roles to replace a reversed deduction using another eligible same-customer voucher. Enforce ticket/original/reversal/new-confirmation relationships and one active deduction server-side; booking cancellation alone gives no replacement authority. No extra Owner approval.

## Optional public-mode trust boundary

Public MSW mode uses simulated role selection, never real login tokens or backend authorization. Persistent visible label: Interactive Demo — Simulated Backend. Synthetic browser records are visitor-editable and cannot prove protection of real data. Existing protected endpoint/RBAC policies above apply to real API mode; mocks do not relax or satisfy them.

Build fixed public/live artifacts on separate origins; unknown mode fails. Public startup awaits mock/storage readiness and blocks business calls on worker/handler failure. Allow static files; deny real API/OIDC and unapproved telemetry fall-through. Keep real secrets and cloud DB configuration out of public assets. Live mode has no mock worker or impersonation controls. Test denial/network/artifact paths under TC-040–045.

Local real API still implements rate limiting, request size/field limits, bounded queries/timeouts and safe logs; record chosen settings/evidence when implemented. These controls are not a claim of full DDoS protection. The plan requires no public tunnel, port forwarding, paid edge service or Azure subscription. Public reset touches only this app's simulated browser dataset and invalidates stale operations/tabs.
