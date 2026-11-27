# Backup and Restore Procedure

Version: 0.1 | Updated: 27 November 2026

Status: Draft — proposed; not implemented or validated

## Current status

No backup or restore has been performed. This proposed runbook must be completed with the exact tested PostgreSQL commands, versions and artifact locations once the database exists.

## Portfolio procedure

1. Record application commit, schema/migration version, PostgreSQL/tool versions and fixture identity.
2. Use a consistent PostgreSQL backup mechanism appropriate to the pinned version, such as a `pg_dump` archive. Back up required schema/data, including ledger, snapshots, audit and idempotency records.
3. Keep credentials outside commands/logs where possible; store backups outside public Git. Record timestamp, size, digest and included scope.
4. Create a separate clean database using compatible restore tooling. Restore required roles/extensions/configuration as documented; do not assume a data backup includes infrastructure or identity-provider configuration.
5. Restore the archive, verify schema and expected row counts, check FK/unique/check constraints, reconcile all voucher balances and run representative read/write smoke checks.
6. Compare restored state with the backup-point manifest, not transactions created after the snapshot. Record duration, failures and corrective actions.
7. Retain evidence and clean up the isolated rehearsal environment safely.

## Restore drill record

| Field | Value |
| --- | --- |
| Backup ID / time / schema / source | Not created |
| File size / SHA-256 | Not available |
| Restore target / tool versions | Not configured |
| Restore start/end and elapsed time | Not measured |
| Row counts and ledger reconciliation | Not checked |
| Smoke-test evidence | Not available |
| Outcome | Not run |

## Later production decisions

Backup frequency, retention, offsite protection, encryption/key access, restore ownership, monitoring, RPO and RTO require explicit business/operational decisions. A synthetic-data dump and one restore drill do not establish a production disaster recovery service.
