# Stakeholders and User Roles

Version: 0.3 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Stakeholder register

| Stakeholder | Proposed responsibility | Participation |
| --- | --- | --- |
| Business owner | Business goals, policies, ownership and pilot decisions | Not agreed |
| Clinic manager | Staff access, package administration and operational review | Not agreed |
| Receptionist | Customer, appointment and purchase workflow feedback | Not agreed |
| Therapist | Service completion and treatment-workflow feedback | Not agreed |
| Customer | Recipient of the clinic service | No MVP account |
| Candy Cheng — Software Engineer | Analysis, design, implementation and technical review | Project owner |
| QA responsibility | Test design, execution, evidence and defect review | Initially the project owner; no separate QA appointment |

## Proposed access boundary

Only Manager and Receptionist are application roles in the MVP. Therapists may be schedulable staff without login accounts. A Customer is a business entity, not an authenticated user.

Both application roles operate within the single clinic. Owner/Manager and Receptionist may both manage customers, appointments, purchases, paper confirmations, deduction rechecks and ticket-linked reversals (Candy, 2026-11-27). Manager-only staff/package administration and sales summaries remain demo assumptions. No extra approval gate applies to ticket reversal. Resource checks still enforce booking/customer/voucher consistency.

The complete [permission matrix](../03-system-design/security-design.md) is proposed. The clinic must validate it before a business pilot. The engineer may choose demo behavior but cannot approve clinic policies or legal/commercial rights on the clinic's behalf.
