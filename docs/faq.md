# Frequently asked questions

## What is MetisCloud?

An Oracle APEX application for OCI cost, usage, resource and workload analysis.
It combines collected reports and inventory with administration and optional
assistant features. Start with the [product overview](../README.md) or
[page directory](pages-and-help.md).

## Is it free for commercial use?

This repository does not grant commercial-use rights. Read the
[PolyForm Noncommercial license](../LICENSE) before using or distributing the
application. OCI infrastructure and optional service charges are separate.

## Is this an Oracle product?

MetisCloud is an independent project built on Oracle technologies. Use this
repository's [support and security routes](../README.md#security-and-support)
for application issues; do not assume an Oracle product-support entitlement.

## What infrastructure do I need?

Oracle Autonomous Database with managed APEX, approved OCI IAM access, network
connectivity and an operator workstation with SQLcl and Bash. The
[installation guide](installation.md) covers database selection and preparation.
The installer does not create the database. Self-managed Database/APEX/ORDS is
outside the documented deployment path.

## Can I use a private endpoint?

Yes. The same bundle and installer apply, but the installer host and browsers
need private routing and DNS. Check outbound OCI service access separately.
Browser CDN dependencies and optional AI calls also need review; a private
database endpoint does not make every connection private.

## Do I need AI or email to start?

No. Leave optional features off while you establish reporting and access.
Enable them after their IAM, configuration and data-handling checks pass.
Follow [Configuration](configuration.md) when ready.

## Does a successful installation mean my data is ready?

No. INITIAL installs the application. [Setup](configuration.md) configures the target
and starts collection. The latest 15 days load first; older history can take
hours. A 100% display with Final processing still means work is running.
Check results before enabling recurring jobs.

## Why can I sign in but not see my workloads?

The User role needs active individual workload assignments. Ask an administrator
to review **User access**. **Awaiting workload assignment** does not mean you
should be given Administrator access. See [Access control](access-control.md).

## Are workspace administrators also MetisCloud administrators?

Not automatically. APEX workspace administration, database ADMIN and the
MetisCloud Administrator role have different responsibilities. The application
uses its configured role-group mappings.

## Why are recent dates or values missing?

Analysis excludes the latest two days. Other missing results can come from
filters, workload permissions, incomplete report availability or delayed loads.
Inventory and billing also come from different sources. Follow
[reporting troubleshooting](troubleshooting.md#reporting-looks-wrong) before
interpreting an empty result as zero cost.

## Does Cloud Advisor change my OCI resources?

No. It displays saved recommendations and recorded evidence, with links to
authorized inventory and matched cost history. It does not implement changes or
record local decisions. Estimates are OCI's supplied values, not proof of
savings; overlapping recommendations must not be added into a portfolio total.
See [Cloud Advisor](usage.md#cloud-advisor).

## Why does an alert remain after I read it?

An active budget condition continues to count even after acknowledgment. The
bell combines active budget alerts with unread non-budget notifications, counting
each once. Reading an alert does not resolve the underlying budget condition.

## Where is the detailed page manual?

Open the page's **?** help button. When enabled, the page-help widget can answer
questions about the current page. Use the [page directory](pages-and-help.md)
to choose a workspace, and the [user guide](usage.md) for reporting examples.

## Can I update from inside the application?

Administrators can use **Import / Update App** with a verified supplied bundle.
Review the plan and arrange a schema backup with the DBA first. The UI cannot
perform INITIAL into an empty environment. See [Upgrade](upgrade.md).

## Will an update keep my settings?

Some settings belong to the target, while shared definitions come from the
release. UPDATE retains target-specific provider references and derived value
lists, but can replace bot prompts, guardrails, semantic definitions and page
help. Review the [ownership table](upgrade.md#what-update-replaces-and-preserves)
and your customizations before applying it.

## Is the bundle a backup? Can I roll back with an older ZIP?

No. A bundle contains application definitions, not a recoverable copy of your
data and environment. Schema backup and recovery are the DBA's responsibility.
The APEX application can be reimported from a compatible release, but INITIAL
cannot run over an existing schema. Installing an older ZIP is not rollback.
See [Backup and recovery](backup-and-recovery.md).

## What should I send when reporting a problem?

Send the version, operation, first failing step and sanitized reproduction details.
Keep raw logs, prompts, customer information, credentials, identifiers and private
URLs out of public issues. Use [Troubleshooting](troubleshooting.md#preparing-a-support-report)
for ordinary defects and [SECURITY.md](../SECURITY.md) for vulnerabilities.
