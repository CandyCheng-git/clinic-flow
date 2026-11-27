# Risk Register

Version: 0.5 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

Impact is an initial qualitative estimate. Owner means proposed responsibility; stakeholder participation has not been agreed.

| ID | Risk | Impact | Mitigation and review trigger | Proposed owner | State |
| --- | --- | --- | --- | --- | --- |
| RK-01 | Scope creep prevents completion | High | Freeze MVP; defer additions; review each milestone | Engineer | Open |
| RK-02 | Unconfirmed clinic rules become hidden assumptions | High | Label assumptions; maintain questions; review before each workflow | Engineer / clinic owner | Open |
| RK-03 | Real customer data or internal material is exposed | High | Synthetic-only public artifacts; review seed, logs and screenshots | Engineer | Open |
| RK-04 | Concurrent requests overdraw a voucher | High | Atomic database operations and real PostgreSQL race tests | Engineer | Open |
| RK-05 | Unauthorized staff change balances | High | Backend policies and active-staff/resource checks | Engineer | Open |
| RK-06 | Stored balance diverges from ledger | High | One transaction per mutation; reconciliation; no history edits | Engineer | Open |
| RK-07 | Architecture work exceeds delivery value | Medium | Modular monolith; build first slice before extra abstractions | Engineer | Open |
| RK-08 | External free service limits block demo | Medium | Check terms before setup; local demo and video fallback | Engineer | Open |
| RK-09 | Vendor-specific configuration reduces portability | Medium | Standard protocols, exported setup notes and containers | Engineer | Open |
| RK-10 | Available hours do not match schedule | High | Record effort; re-estimate after first slice | Engineer | Open |
| RK-11 | AI-generated code is accepted without understanding | High | Review, explain and test critical paths | Engineer | Open |
| RK-12 | IP, branding or commercial use is disputed | High | Confirm rights before clinic-specific publication or pilot | Engineer / clinic owner | Open |
| RK-13 | Backup exists but cannot be restored | High | Restore into clean database and reconcile | Engineer | Open |
| RK-14 | DST creates incorrect appointment instants | Medium | Explicit time zone handling and transition tests | Engineer | Open |
| RK-15 | Paper-to-POS re-entry may delay or omit entries, or enter a slip twice | High | Confirm entry ownership, timing, pending/entered checks and reconciliation; no incident or rate established | Engineer / clinic manager | Open |
| RK-16 | Paper, POS and ClinicFlow identifiers may be matched to the wrong customer | High | Validate source-labelled ID mapping and duplicates before pilot/import; preserve original reference values | Engineer / clinic manager | Open |
| RK-17 | Parallel paper/POS/ClinicFlow records may disagree | High | Agree authoritative records, cut-off and discrepancy resolution before operational trial | Engineer / clinic owner | Open |
| RK-18 | Staff paper confirmation is missing, stale or mistaken | High | Bind frozen usage summary/reference/actor/time; no image upload; require fresh confirmation for signed-detail changes; test missing/stale paths | Engineer / clinic owner | Open |
| RK-19 | Saved completion/signature or lost response is mistaken for successful deduction | High | Separate evidence and operation outcome; Pending/ReviewRequired recovery, original-operation lookup and idempotency | Engineer | Open |
| RK-20 | Broad reversal permission or duplicate tickets restore sessions incorrectly | High | Enforce active staff, original ticket/reason, unique reversal and atomic ticket/ledger audit; both-role permission is user-specified | Engineer | Open |
| RK-21 | Mock and real modes are confused or fallback exposes real services | High | Fixed artifacts/origins, persistent simulation labels, deny unhandled business requests, network/build checks TC-040/041/045 | Engineer | Open |
| RK-22 | Mock fixtures, browser persistence or reset diverge from core correction rules | High | Shared contracts/scenarios, versioned coherent seed, generation-aware reset/tab coordination, TC-042–044 | Engineer | Open |
| RK-23 | Optional simulation scope or hosting limits delay/overrun portfolio delivery | Medium | Core first, optional mock milestones, release-time plan/billing review and local/video fallback | Engineer | Open |

Review open high-impact risks before each release gate. Record evidence, residual risk and review date when a mitigation is verified; do not close a risk merely because it appears in a document.
