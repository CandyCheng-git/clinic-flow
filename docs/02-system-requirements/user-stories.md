# User Stories

Version: 0.5 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

All stories are proposed. Acceptance detail is in [acceptance-criteria.md](acceptance-criteria.md). Delivery starts with the voucher vertical slice; story numbering does not dictate implementation order.

| ID | Story | Priority | Requirement IDs | Acceptance group |
| --- | --- | --- | --- | --- |
| US-01 | As a Receptionist, I want to find or create a customer so I can use one profile across bookings and vouchers | Must | FR-C01–FR-C04 | AC-C |
| US-02 | As a Receptionist, I want to see customer history and archive inactive profiles without losing transactions | Must | FR-C05–FR-C07 | AC-C |
| US-03 | As a Receptionist, I want a daily/weekly calendar and conflict-safe appointments | Must | FR-B01, FR-B02, FR-B05, FR-B06 | AC-B |
| US-04 | As Owner or Reception, I want lifecycle and paper-signature confirmation so completed signed usage deducts automatically | Must | FR-B03, FR-B04, FR-B07, FR-B08 | AC-B, AC-S |
| US-05 | As a Manager, I want to maintain services and packages without changing purchased entitlements | Must | FR-V01, FR-V02 | AC-P |
| US-06 | As a Receptionist, I want to record a package purchase and show its original price and sessions | Must | FR-V03, FR-V04 | AC-P |
| US-07 | As Owner or Reception, I want signed completed usage to deduct once automatically despite retries/concurrency | Must | FR-V05–FR-V07, FR-V10 | AC-V |
| US-08 | As Owner or Reception, I want to reverse a mistake through a ticket and replace it using another correct voucher, preserving the full chain | Must | FR-V08, FR-V09, FR-V11, FR-V12, FR-V13 | AC-V, AC-T |
| US-09 | As a staff user, I want secure login and role-appropriate access | Must | FR-A01–FR-A06 | AC-A |
| US-10 | As a Manager, I want defined sales and session summaries with date filtering and export | Should | FR-R01–FR-R06 | AC-R |
| US-11 | As the engineer, I want to detect ledger discrepancies and restore a backup | Must | NFR-003, NFR-009, NFR-010 | AC-O |
| US-12 | As Owner or Reception, I want to save incomplete work and recheck failed/unknown deductions without lost evidence or double deduction | Must | FR-B09, FR-B10 | AC-D |
| US-13 | As a recruiter, I want a clearly labelled synthetic interactive demo with simulated roles and reset, while real backend evidence remains separately inspectable | Conditional presentation | FR-D01–FR-D06 | AC-DM |

Both roles have routine workflow and ticket reversal rights specified by Candy (2026-11-27); other Manager-only restrictions remain demo assumptions.

Break each story into small implementation tasks only when its acceptance group is reviewed. Retain unresolved business decisions rather than silently adding them to code.
