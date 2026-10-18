# Troubleshooting

Record release version, UTC time, operation and run/request ID before changing
anything. Keep the exact error privately and share only sanitized evidence.

## Bundle validation fails

```bash
shasum -a 256 -c bundle-app.zip.sha256
```

A checksum mismatch means the ZIP is damaged or modified. Obtain the approved
artifact again; do not edit its contents. Unsafe paths, duplicate members,
unsupported contracts or registry/runtime disagreement also require a corrected
release. If only the sidecar fails, confirm ZIP and checksum came together.

## SQLcl or browser connection fails

Test the appropriate saved connection in a fresh terminal:

```bash
sql -name MC_TARGET
```

For installation use the ADMIN saved connection instead. Check SQLcl, the
protected wallet/connection definition, database state, DNS, network access and
certificates. Never put a password on the command line.

SQL connectivity does not prove browser HTTPS access. For a private endpoint,
check both routes from their actual client networks. Do not disable TLS checks
or open access to every address to work around a failure.

## INITIAL fails

Before schema creation, inspect names, creation confirmation, workspace choice,
offset and database prerequisites. On a shared database, obtain DBA approval for
missing database-wide settings.

After mutation begins, preserve the private journal. Do not restart INITIAL
against the partial schema. Use [failed-install cleanup](recovery-and-removal.md)
with the original journal and ADMIN connection, and review what it removed.

## UPDATE stops

An unsupported engine requires supported migration instructions. Revision,
lineage and unowned-object conflicts require investigation, not a forced update.
A missing privilege requires a separately reviewed DBA action; UPDATE cannot
use the old ADMIN remediation flags.

For partial execution, inspect Runs and the first failed object. Ask the DBA to
review compilation and current receipt state before retrying. Oracle DDL may
already have committed. See [Upgrade](upgrade.md) and
[Backup and recovery](backup-and-recovery.md).

## Sign-in or workload access fails

Confirm target app ID/workspace/offset and the intended authentication scheme.
Do not guess a lost offset or disable authorization.

| Symptom | Check |
| --- | --- |
| Workspace administrator cannot administer MetisCloud | These are separate roles; verify the application role group. |
| Awaiting workload assignment | Administrator must save and activate individual assignments. |
| Pending SSO assignments do not activate | Exact case-sensitive identity, first-sign-in role and any required review. |
| Role change not visible | Start a new session and review assignments after the change. |
| Disabled user still expected to have access | Local disabling is independent of the APEX/OCI account; inspect local state. |

Keep SSO identities unique to their original people. Test a separate ordinary
session after access changes; administrator visibility proves nothing about
restricted-user scope.

## Setup or ingestion stalls

NOT_TESTED and SKIPPED connectivity checks are not passes. Check report
availability, target report location, IAM, Resource Principal and network access
from the target schema's perspective. A policy change can take time to propagate;
see Oracle's [Resource Principal guidance](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/resource-principal.html).

The recent 15-day load completes before older history. A 100% display with
Final processing is still running. Failed history keeps nightly cost ingestion
on hold. Correct its cause before using the supported retry.

For RUNNING records, verify actual execution. Stop job applies only to verified
Scheduler work. Mark failed is appropriate only after execution ended and
requires an audited reason. Do not guess a session to terminate or queue
duplicate loads to make progress appear.

## Reporting looks wrong

Check currency, period, billing account, workload assignments and filters.
Analysis excludes the latest two days. Compare Daily Loads with source-file
availability; a successful empty load is not proof of complete billing data.

Inventory and billing are different sources. Check the resource's identity and
period before expecting cost. For workload discrepancies, review exact
compartment selection, tag keys and hierarchy refresh status. Partial topology
coverage does not prove missing relationships. Missing availability metrics
affect the headline calculation; they do not independently prove an outage.

## Assistant or spreadsheet functions fail

For missing charts, icons, PDF or Excel output, inspect browser network failures
for blocked dependencies. Do not weaken security controls. See
[third-party notices](../THIRD_PARTY_NOTICES.md).

For assistant problems, verify approved model/service access and populated,
approved dataset metadata. Inspect the relevant Native/Generic run in Bot Logs.
An incomplete log is not a conclusive diagnosis. Page-help widget failures have
WID references searchable in Widget Insights. See
[Assistant administration](assistant-administration.md).

## Email or file download fails

Verify target app/workspace settings, gateway URL, approved sender, Resource
Principal and enabled recipient mapping. Background work cannot rely on a browser
session. Test a safe recipient before enabling system mail.

Inspect Email Queue Details for error category, attempts and request ID.
Held mail has an uncertain outcome and is not automatically retryable. Confirm
delivery with the operator before another action. Report files expire after
seven days; a missing expired file requires a new report request.

## Preparing a support report

Include versions of MetisCloud, bundle, OS, Bash and SQLcl, mode/operation,
first failing step, sanitized error and reproducible steps. Never publish
wallets, credentials, connection definitions, customer identifiers, private
URLs, raw prompts/results or complete runtime logs.

Use [GitHub Issues](https://github.com/andreT30/metiscloud/issues) or
[GitLab Issues](https://gitlab.com/andreT30/metiscloud/-/issues) for non-sensitive
defects on your preferred host, and [SECURITY.md](../SECURITY.md) for vulnerabilities.
If the safe correction is unknown, stop rather than granting broad access or modifying
bundle SQL.
