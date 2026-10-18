# MetisCloud documentation

New to MetisCloud? Watch the [product overview](media/metiscloud-product-overview.mp4),
then use the path that matches your role. The [page directory](pages-and-help.md)
explains what each area does and where to find detailed help inside the application.

## Install and set up

1. [Install MetisCloud](installation.md): prepare the database, networks, IAM and
   SQLcl connection; download and verify the distribution; run INITIAL and open
   the installed application.
2. [Set up and configure MetisCloud](configuration.md): sign in as the first
   administrator, complete Setup, verify the first load, add users and enable
   recurring work. The same guide covers optional AI/email and later settings changes.

Each guide has a contents list for returning to a particular step. For an existing
installation, use [Upgrade](upgrade.md).

## Use the application

- [Pages and in-app help](pages-and-help.md): find the right workspace and its help.
- [User guide](usage.md): investigate spending, use inventory, save reports and
  interpret availability or assistant results.
- [FAQ](faq.md): common questions about deployment, access, data and maintenance.

## Configure and operate it

| Task | Guide |
| --- | --- |
| Change target settings, AI or email | [Set up and configure MetisCloud](configuration.md) |
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
- [License](../LICENSE), [third-party notices](../THIRD_PARTY_NOTICES.md),
  [vulnerability reporting](../SECURITY.md) and [contributing](../CONTRIBUTING.md).

Examples use placeholder names. Keep real credentials, connection definitions,
OCI identifiers and operational logs outside this repository.
