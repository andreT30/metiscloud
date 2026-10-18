# MetisCloud documentation

New to MetisCloud? Watch the [product overview](media/metiscloud-product-overview.mp4),
then use the path that matches your role. The [page directory](pages-and-help.md)
explains what each area does and where to find detailed help inside the application.

## Install an environment

1. [Infrastructure](infrastructure.md): provision the database and check access paths.
2. [Requirements and preparation](prerequisites.md): check compatibility, IAM,
   network access and workstation tools.
3. [Installation](installation.md): download the distribution, verify the bundle,
   run INITIAL and locate the installed application.
4. [First sign-in and Setup](first-use.md): configure the target, load data and
   check a report against its source.
5. [Access control](access-control.md): assign roles and workloads, then test access.
6. [Acceptance checklist](acceptance-checklist.md): record your environment's results.

The installer does not provision the Autonomous Database. Arrange that first
with your OCI administrator. AI and email are optional; enable them only after
their access and data-handling requirements are approved.

## Use the application

- [Pages and in-app help](pages-and-help.md): find the right workspace and its help.
- [User guide](usage.md): investigate spending, use inventory, save reports and
  interpret availability or assistant results.
- [FAQ](faq.md): common questions about deployment, access, data and maintenance.

## Configure and operate it

| Task | Guide |
| --- | --- |
| Change target settings, AI or email | [Configuration](configuration.md) |
| Manage roles, groups and workload assignments | [Access control](access-control.md) |
| Manage schedules, loads, workloads and email queues | [Administration](administration.md) |
| Maintain assistant datasets, models and diagnostics | [Assistant administration](assistant-administration.md) |
| Review security boundaries and retention | [Security](security.md) |
| Install a later supplied bundle | [Upgrade](upgrade.md) |
| Prepare a checkpoint or recover | [Backup and recovery](backup-and-recovery.md) |
| Clean up failed INITIAL or uninstall | [Recovery and removal](recovery-and-removal.md) |
| Investigate failures | [Troubleshooting](troubleshooting.md) |

For routine operation, check load results and failed jobs, review access changes,
and investigate email or assistant failures. Before maintenance, agree a recovery
checkpoint and a window that does not overlap ingestion. The administration and
upgrade guides give the steps and checks.

## Release and project information

- [Version](../VERSION) and [changelog](../CHANGELOG.md).
- [Deployment checklist](acceptance-checklist.md).
- [License](../LICENSE), [third-party notices](../THIRD_PARTY_NOTICES.md),
  [vulnerability reporting](../SECURITY.md) and [contributing](../CONTRIBUTING.md).

Examples use placeholder names. Keep real credentials, connection definitions,
OCI identifiers and operational logs outside this repository.
