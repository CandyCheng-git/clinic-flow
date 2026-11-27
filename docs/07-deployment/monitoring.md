# Monitoring and Operational Visibility

Version: 0.1 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Proposed logging

Capture correlation/request ID, operation/route template, outcome, duration, safe error code, authentication/authorization failures and transaction failures. Use structured fields. Business audit entries describe the actor/action/entity and correction reason without copying complete customer records.

Never log passwords, full tokens, API keys, connection strings, payment details or full request bodies. Restrict access to detailed logs. Synthetic data does not justify insecure logging habits.

## Signals and interpretation

| Signal | Purpose | Current state |
| --- | --- | --- |
| Liveness | Process responds without exposing internals | Not implemented |
| Readiness / database connectivity | Application can serve required operations | Not implemented |
| Error rate and request duration | Detect failed/slow operations by route | Not measured |
| Transaction conflict/failure counts | Separate expected business conflicts from infrastructure failures | Not measured |
| Resource usage | Diagnose CPU/memory/database pressure | Not measured |
| Ledger reconciliation | Detect balance projection discrepancies | Not implemented |
| Backup and restore outcomes | Identify failed recovery preparation | Not measured |

Keep public health output minimal; protect detailed dependency diagnostics. A rejected zero-balance redemption is a business conflict, not automatically an infrastructure incident. A ledger discrepancy requires investigation and must not be silently repaired by overwriting history.

## Initial operating practice

Review logs after each demo rehearsal, attach safe evidence to defects and verify request IDs link UI errors to server events. Document alert thresholds only after baseline behavior is measured. The portfolio has no uptime commitment, on-call service or production monitoring agreement.
