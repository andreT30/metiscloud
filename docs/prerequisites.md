# Prepare the environment

This release targets Oracle Autonomous Database with managed Oracle APEX.
Public and private endpoints use the same bundle and installer. Self-managed
Database, APEX and ORDS are outside this guide. Complete this checklist before
[installation](installation.md).

## People and access

| Owner | Prepare |
| --- | --- |
| OCI administrator | Deployment compartment, database, approved data scope, dynamic group, service-specific IAM policies and network access. |
| DBA | ADMIN access, database capabilities, automatic indexing, recovery point and maintenance window. |
| APEX workspace administrator | Workspace access, authentication and application user/group setup. This is separate from database ADMIN. |
| Application administrator | Reporting scope, regions, tags, first-load period, recipients and job approval. |

Keep actual OCIDs, endpoints, connection details and approvals in a private
deployment record outside this repository.

## Database compatibility

Provision the database in the selected compartment/region using Oracle's
[provisioning guide](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/autonomous-provision.html).
Choose compute/storage and backup retention for the workload, review autoscaling
costs, and choose restricted public access or a private endpoint. Confirm managed
APEX is enabled in the database tools configuration. Provisioning is an OCI
administrator action, not an installer phase.

Use an Autonomous Database capable of the bundle's SQL property graphs and
native JSON objects, with managed APEX available. The supplied application requires
compatibility with **APEX 26.1.5**; do not assume it imports into older versions. Record
actual database, APEX and SQLcl versions in the [acceptance checklist](acceptance-checklist.md).
Do not infer compatibility with other database or APEX versions from a successful
connection alone.

Database-wide automatic indexing and Resource Principal settings require DBA
approval. INITIAL database mode NEW authorizes prerequisite configuration;
EXISTING checks the shared database and requires separate approval for changes.
Review the [standalone installer](../db/deployer/install_mc_bundle.sh), including
property-graph and context privileges. Do not assume a restricted or free service
tier meets these requirements.

Choose an unused schema name, workspace name and non-conflicting application ID.
INITIAL creates the schema; do not create an empty schema in advance.
The examples use `MC_TARGET`, `MC_WORKSPACE` and app ID `2300`; these are examples,
not required identities.

## Public or private networking

Check three independent paths. SQL connectivity does not prove browser or
outbound service connectivity.

| Path | Public endpoint | Private endpoint |
| --- | --- | --- |
| Installer host → database | Permit the host through the configured access controls; use approved TLS/mTLS settings. | Use a host with private routing and DNS to the database or an approved connected network. |
| Browser → managed APEX | Verify the managed APEX URL under the intended access policy. | Verify private APEX DNS and HTTPS connectivity from the browser's network; database-port forwarding alone is insufficient. |
| Database → OCI services | Check service endpoint access and IAM. | Also check outbound routes, DNS, security rules and applicable private-endpoint outbound settings. |

Keep certificate validation enabled. Follow Oracle's [private endpoint instructions](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/private-endpoints-autonomous.html)
and [APEX web-service guidance](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/apex-web-services.html).
Do not open access to every address to work around a failed test. Browser
dependencies and optional integrations are covered in [Security](security.md).

## Resource Principal and IAM

Create a dynamic group selecting the intended Autonomous Database. For a single
installation, scope membership to that resource rather than every database in a
compartment. Approve service permissions, allow for IAM propagation, and test
from the target schema, not only a human administrator account. Oracle documents
[Resource Principal setup](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/resource-principal.html).
The installer enables database access but cannot create tenancy policies.

### Example: select one database

In OCI Identity and Security, create a dynamic group for the application database.
For example, name it `MetisCloudDatabase` and use this matching rule:

```text
resource.id = '<ADB_OCID>'
```

Replace `<ADB_OCID>` with the database OCID from its details page, not the
compartment or tenancy OCID. Follow Oracle's Resource Principal instructions
above for your identity-domain configuration. Review membership before granting
permissions: the database identity receives these permissions, not an individual
MetisCloud user.

### Example: allow cost-report reads

Create the following policy in your tenancy's root compartment. Replace
`<DYNAMIC_GROUP>` with your dynamic-group name, qualified with its identity
domain where required. Obtain `<ORACLE_REPORT_TENANCY_OCID>` from Oracle's
[cost-report cross-tenancy policy example](https://docs.oracle.com/en-us/iaas/Content/Functions/Tasks/functions_pbf_catalog_cost_reports_focus_converter.htm)
(Permissions section).
It identifies Oracle's report store, not your own tenancy.

```text
Define tenancy usage-report as <ORACLE_REPORT_TENANCY_OCID>
Endorse dynamic-group <DYNAMIC_GROUP> to read objects in tenancy usage-report
```

Only the report-read policy is needed from that example; MetisCloud does not
require deploying the converter function. Confirm the report-store identifier
applies to your OCI realm. This policy permits report reads; it does not grant
inventory, subscription, Monitoring or optional service access.

### Review permissions for each capability

| Capability | Permission boundary to review |
| --- | --- |
| FOCUS report ingestion | Read your reports from the Oracle-owned report store. Follow Oracle's [Cost Reports policy instructions](https://docs.oracle.com/en-us/iaas/Content/Billing/Concepts/costusagereportsoverview.htm); the report-store tenancy is not your tenancy. |
| Inventory | Inspect/read selected compartments and resource types across the intended regions. |
| Subscription/credits | Read the relevant subscription and usage data. |
| Availability | Read required Monitoring metrics and resource metadata. |
| AI (optional) | Invoke an approved model/service in the selected region and compartment. |
| Email (optional) | Discover configuration and send using an approved sender in its compartment. |
| Scaling/operational actions (optional) | Separate, explicitly approved permissions, not prerequisites for ordinary reporting. |

This is a permission-planning checklist, not a universal IAM policy. Required
statements depend on enabled features and tenancy scope. Do not grant blanket
`manage all-resources` access. Retain the reviewed policy and a successful
target-side check for each enabled capability with your deployment record.

## Workstation and saved connection

The installer requires Bash 3.2+, Oracle SQLcl (`sql`), a Java 17+ runtime,
`zip`, `base64`, a SHA-256 utility and standard POSIX tools.
The archive reader is embedded in the installer; it does not download code.
It looks for SQLcl's bundled Java runtime, configured Java locations such as
`JAVA_HOME`, or `java` on `PATH`. No Java compiler is required.
On Windows use a POSIX Bash environment with paths SQLcl can access; native
PowerShell is not the installer shell. Check SQLcl's own Java
requirements for the version installed:

```bash
bash --version
sql -version
```

Configure the Autonomous Database wallet/cloud connection outside this checkout.
For an mTLS connection:

1. On the database details page, open **Database connection** and download the
   instance wallet using your organization's credential-handling procedure.
2. Store the wallet ZIP in a private directory outside the distribution. Keep
   its path stable; the saved connection must still work in later sessions.
3. Choose a service alias from that database's connection information. Replace
   `YOUR_ADB_SERVICE_ALIAS` below with that alias.
4. Start `sql /nolog`, then configure the wallet and save the ADMIN connection:

```text
SET CLOUDCONFIG /path/to/private/Wallet_database.zip
CONNECT -save TARGET_ADMIN -savepwd ADMIN@YOUR_ADB_SERVICE_ALIAS
```

Replace the wallet path and enter the password only at its prompt. For an
already configured wallet/TNS connection, use its existing configuration.
If your organization uses walletless TLS, follow its approved connection setup
instead of downloading an mTLS wallet. Oracle describes
[SQLcl wallet connections](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/connect-sqlcl.html) and
[stored connections](https://docs.oracle.com/en/database/oracle/sql-developer-command-line/24.4/sqcug/managing-stored-connections.html).

Exit SQLcl and test the saved connection in a fresh terminal:

```bash
sql -name TARGET_ADMIN
```

At the SQLcl prompt, run `SHOW USER`. It must identify `ADMIN`. Then run `EXIT`
to return to the shell before starting the installer. If reconnecting fails,
fix the wallet path, saved connection or network access before continuing.
Protect the saved connection and wallet. INITIAL creates the separate target
saved connection. Do not substitute SQL*Plus for SQLcl.

## Ready to install

- [ ] Database and managed APEX accessible from the intended networks.
- [ ] Database capabilities and automatic indexing approved by the DBA.
- [ ] ADMIN saved connection tested; workspace administrator available.
- [ ] IAM policies approved for the selected services and scope.
- [ ] Target schema/workspace/app identity chosen.
- [ ] Recovery owner, capacity and service costs approved.
- [ ] Bundle and sidecar obtained from the same release.

Continue with [Installation](installation.md), then [First use](first-use.md).
