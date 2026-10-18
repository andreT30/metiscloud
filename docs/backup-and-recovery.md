# Backup and recovery

Before UPDATE or a significant configuration change, agree acceptable data loss
and recovery time with the DBA. A release bundle contains application definitions;
it is not a backup of your data or environment settings.

## Prepare a recoverable checkpoint

1. Notify users and control new writes. Pause conflicting schedules, record their
   prior states, and wait for active work or stop it deliberately.
2. Record a UTC cutoff. Confirm an available Autonomous Database backup or
   recovery point covers it; a configured policy alone is not proof.
3. Confirm the recovery scope includes application schema data/objects, APEX
   application/workspace state, user access, configuration and scheduler state.
   Have the DBA document any platform components needing separate recovery.
4. Retain the installed release/checksum and deployment output privately. Record
   customized bot prompts, guardrails and semantic definitions, plus target
   provider references, so you can review changes after UPDATE. These records
   support review; they do not replace a recoverable database checkpoint.
5. Preserve external IAM, identity-provider, network/DNS settings and required
   Object Storage files under their own recovery policies.
6. Assign the recovery operator and rehearse the procedure in isolation.

Encrypt backups and restrict access. Never commit operational backups here.
Use Oracle's [backup and recovery guidance](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/backup-restore.html).
External Object Storage files need their own protection; restoring the database
does not restore them.

## Failed UPDATE

Oracle DDL can commit before a later failure. Preserve the run ID, failed object,
receipts and first Oracle error. Do not assume that a cancelled or failed run
reverted earlier changes.

| Situation | Next action |
| --- | --- |
| Validation stopped before mutation | Verify the log confirms that boundary, correct the prerequisite, then validate again. |
| Partial changes with understood reconciliation | DBA reviews current state and approves correction/retry of the same candidate. |
| Unknown drift, data loss or unsafe retry | Stop changes and use the tested restore procedure. |

Installing an older bundle is not rollback. CLEANUP is for failed INITIAL
attempts, not an installed environment or failed UPDATE.

## Restore safely

Test recovery on an isolated clone before relying on it. Prevent restored jobs
and production email from running before review. A clone has its own OCI resource
identity and needs an IAM review.

For an approved whole-database restore, follow Oracle's
[restore procedure](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/backup-recover.html).
The database is unavailable during restore and every application in it is
affected. Obtain the database owner's approval, especially on shared databases.

After restore, keep traffic controlled while you:

1. Inspect scheduler and outgoing-email state.
2. Verify application/database consistency, workspace mapping, target identity
   and authentication.
3. Test a known report and both allowed and denied workload access.
4. Test optional AI/email with safe destinations.
5. Resume approved schedules, monitor their next run, then reopen access.

Record the recovered cutoff, lost transactions and elapsed recovery time.
See [Acceptance](acceptance-checklist.md) for the environment-specific record.
