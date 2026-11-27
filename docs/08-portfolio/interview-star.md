# Interview STAR Drafts

Version: 0.4 | Updated: 27 November 2026

Status: Future answer templates — no implementation achievements claimed

**These are preparation prompts and future answer templates. Do not present them as completed work.** Current truthful wording: “I am designing a beauty-clinic portfolio system around voucher transaction integrity. The implementation and test results are still pending.”

## STAR 1 — Concurrency and data integrity

| Element | Draft to complete after implementation |
| --- | --- |
| Situation | A synthetic clinic scenario allowed two staff requests to compete for one remaining treatment session |
| Task | Prevent negative balances and retain a consistent transaction history |
| Action | Describe the transaction/locking code you actually wrote, alternatives you evaluated and real PostgreSQL tests you executed |
| Result | Insert actual success/conflict counts, final persisted balance and evidence; currently not available |

Possible English structure after verification: “In my ClinicFlow portfolio project, I tested [actual scenario]. I implemented [actual approach] and verified it using [actual test setup]. The result was [observed outcome], with [limitation].”

## STAR 2 — Retry safety and correction

| Element | Draft to complete after implementation |
| --- | --- |
| Situation | A lost API response could cause a staff member to retry; incorrect deductions needed traceable correction |
| Task | Avoid repeated effects and preserve history |
| Action | Explain your implemented key scoping, payload hashing, booking active-pointer/operation constraints and both-role ticket-linked reversal/replacement preserving the full correction chain |
| Result | Use executed retry/reversal tests and actual ledger evidence; currently not available |

## STAR 3 — Requirements and business assumptions

| Element | Draft to complete after review or validation |
| --- | --- |
| Situation | The initial workflow identified bookings, purchases and balances but left policies unresolved |
| Task | Make the demo scope implementable without claiming unconfirmed clinic rules |
| Action | Identify the assumptions you recorded and any stakeholder discussions you actually held |
| Result | Report the reviewed scope/decision and its evidence; do not invent clinic participation |

## Likely follow-up questions

- Why was the database lock acquired before checking the balance?
- What distinguishes an idempotency key from a unique booking rule?
- What if the response is lost after commit?
- What rolls back when ledger or audit persistence fails?
- Why is an ID token unsuitable for API authorization?
- How does staff deactivation affect an already-issued access token?
- What did you measure, on what data and under what load?
- Which parts are demo assumptions, and which did a clinic validate?

Use first-person past tense only for work actually performed. Do not add percentage improvements, production users or commercial adoption without evidence.
