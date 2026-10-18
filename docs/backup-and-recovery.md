# Backup and recovery

Backup and recovery of the MetisCloud database schema are the DBA's
responsibility, using database administration tools and your organization's
backup policy. MetisCloud does not create schema backups or restore them.
The supplied application bundle can reinstall application definitions; it does
not recover collected data or runtime configuration.

## Schema backup

Before UPDATE or a significant configuration change, arrange a recoverable
schema backup with the DBA. Agree the recovery cutoff and acceptable data loss.
Pause conflicting schedules, control new writes and wait for active work to
finish or stop it deliberately. Record the prior scheduler states.

The recovery procedure must cover the schema's objects and data, including
runtime settings, user-access records, deployment records and scheduler state.
The DBA chooses and tests the backup method, including any required grants and
dependencies. Oracle documents
[schema backup with Data Pump on Autonomous Database](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/export-data-create-dump-file.html).

Retain the matching release bundle and checksum, application ID, APEX offset,
workspace and parsing-schema mapping in a private deployment record. Workspace
users, credentials, identity-provider configuration, OCI IAM, network settings
and external files may need separate recovery arrangements. Do not assume they
are included in a backup of the application schema.

Protect backups under your organization's access, encryption and retention
policies. Keep them outside this repository.

## Recover the schema

The DBA restores the application schema using the tested database recovery
procedure. Keep user access, scheduled jobs and outgoing email controlled
during recovery and validation.

If recovering into another database, review database prerequisites, privileges,
Resource Principal membership and IAM permissions for the new target. Confirm
the APEX workspace mapping and application identity before reopening access.

## Reinstall the APEX application if needed

An APEX administrator can reimport the application definition from a release
compatible with the recovered schema. Preserve the intended application ID,
component offset, workspace and parsing-schema mapping. Oracle's
[application import guide](https://docs.oracle.com/en/database/oracle/apex/26.1/htmdb/importing-export-files.html)
describes the APEX import procedure. Reimporting the application does not restore
schema data or independently recreate workspace users and credentials.

INITIAL creates a fresh installation and rejects an existing schema. Do not run
it over a recovered schema. Use [Installation](installation.md) only when you
intend to create a new environment, or [Upgrade](upgrade.md) for a compatible
installed target.

## Failed UPDATE

Oracle DDL can commit before a later failure. Preserve the run ID, failed object,
receipts and first Oracle error. Do not assume that a cancelled or failed run
reverted earlier changes.

| Situation | Next action |
| --- | --- |
| Validation stopped before mutation | Verify the log confirms that boundary, correct the prerequisite, then validate again. |
| Partial changes with understood reconciliation | DBA reviews current state and approves correction/retry of the same candidate. |
| Unknown drift, data loss or unsafe retry | Stop changes and have the DBA recover the schema from its backup. |

Installing an older bundle is not rollback. CLEANUP is for failed INITIAL
attempts, not an installed environment or failed UPDATE.

## Return to service

After schema recovery and any required APEX reimport, verify application/schema
version compatibility, workspace mapping, authentication and target settings.
Check a [known report](configuration.md#verify-the-first-load) and both allowed
and denied [user access](configuration.md#give-other-users-access). Test optional
AI and email with approved inputs and recipients.

Review scheduler and outgoing-email state before resuming the intended jobs.
Monitor their next runs, then reopen access. Record the recovered cutoff, data
loss and recovery result privately.
