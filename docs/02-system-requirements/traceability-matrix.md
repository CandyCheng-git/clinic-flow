# Requirement Traceability Matrix

Version: 0.7 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

This initial matrix groups related functional requirements. Expand it to individual implementation tasks as code is added. `Planned` means there is no implementation commit or executed verification evidence yet.

| Business requirement | Functional requirement(s) | Rule / quality requirement | Story | Test case(s) | Implementation / evidence |
| --- | --- | --- | --- | --- | --- |
| BR-001 | FR-C01–FR-C04 | NFR-013 | US-01 | TC-013 | Planned |
| BR-001 | FR-C05–FR-C07 | NFR-004 | US-02 | TC-014 | Planned |
| BR-002 | FR-B01, FR-B02, FR-B05, FR-B06 | RULE-003 | US-03 | TC-008, TC-012, TC-015 | Planned |
| BR-002 | FR-B03, FR-B04, FR-B07 | RULE-009, RULE-010, RULE-014 | US-04 | TC-016, TC-029 | Planned |
| BR-003 | FR-V01–FR-V03 | RULE-013 | US-05, US-06 | TC-017, TC-018 | Planned |
| BR-004 | FR-V04, FR-V05, FR-V10 | RULE-001, RULE-002, RULE-015 | US-07 | TC-001, TC-002, TC-010, TC-019 | Planned |
| BR-005 | FR-V06 | RULE-004 | US-07 | TC-003, TC-020, TC-021 | Planned |
| BR-005 | FR-V07 | RULE-001; NFR-003 | US-07 | TC-002, TC-004, TC-005 | Planned |
| BR-006 | FR-V08, FR-V09, FR-V11, FR-V12, FR-V13 | RULE-005, RULE-006, RULE-007; DA-07; DEC-20261127-06 | US-08 | TC-009, TC-022, TC-033, TC-034, TC-037, TC-038, TC-039 | Planned |
| BR-007 | FR-A01, FR-A02, FR-A05 | NFR-001 | US-09 | TC-006, TC-023 | Planned |
| BR-007 | FR-A03, FR-A04, FR-A06 | RULE-007; NFR-001 | US-09 | TC-007, TC-024 | Planned |
| BR-008 | FR-R01–FR-R05 | NFR-004 | US-10 | TC-025 | Planned |
| BR-009 | FR-R06 | NFR-001, NFR-013 | US-10 | TC-026 | Planned |
| BR-004, BR-006 | FR-V04, FR-V08 | NFR-009, NFR-010 | US-11 | TC-011, TC-027 | Planned |
| Cross-cutting | Read APIs | NFR-002 | US-01, US-03, US-10 | TC-028 | Planned |
| BR-002, BR-004, BR-005 | FR-B08, FR-V05, FR-V10 | RULE-014; NFR-003 | US-04, US-07 | TC-029, TC-030 | Planned |
| BR-002, BR-004, BR-005 | FR-B09, FR-B10 | NFR-003, NFR-007 | US-12 | TC-031, TC-032 | Planned |
| BR-007 | FR-A01 | NFR-001 | US-09 | TC-035 | Planned |
| BR-001, BR-008, BR-009 | FR-C03, FR-R05, FR-R06 | NFR-006 | US-01, US-10 | TC-036 | Planned |
| BR-004, BR-005, BR-006 | FR-B08, FR-B10, FR-V05–FR-V09, FR-V13 | NFR-003, NFR-004; DB-001–DB-009 | US-07, US-08, US-12 | TC-046, TC-047, TC-048, TC-050 | Planned; reviewed design only |
| BR-003 | FR-V02, FR-V03 | RULE-013; DB-010 | US-05, US-06 | TC-049 | Planned |
| Portfolio: DEC-20261127-07 | FR-D01–FR-D06 | NFR-013, NFR-014 | US-13 | TC-040, TC-041, TC-042, TC-043, TC-044, TC-045 | Conditional; Planned |
| BR-010 | Historical import | Deferred | Future | Not planned for MVP | Deferred |
| BR-011 | Multi-branch | Out of scope | Future | Not planned for MVP | Deferred |
| BR-012 | Reminders | Out of scope | Future | Not planned for MVP | Deferred |

Use [test-cases.md](../06-testing/test-cases.md) for scenario definitions and [test-report.md](../06-testing/test-report.md) for actual outcomes. After implementation, add a commit or PR and evidence path; a test name without an execution result is not verification.

Each FR has a checkable example and AC/TC reference in [SRS](SRS.md). New cases remain Not run; both-role ticket reversal supersedes the Manager-only assumption.
