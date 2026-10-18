# Prepare the environment

This release targets Oracle Autonomous Database with managed Oracle APEX.
Public and private endpoints use the same bundle and installer. Self-managed
Database, APEX and ORDS are outside this guide. Prepare the following before
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
Strongly prefer **Autonomous AI Lakehouse**, formerly Autonomous Data Warehouse,
for MetisCloud's batch loads and analytical queries. Select **Lakehouse** as the
workload type for a new deployment; see Oracle's
[workload types](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/about-autonomous-database-workloads.html).
Choose compute/storage and backup retention for the workload, review autoscaling
costs, and choose restricted public access or a private endpoint. Confirm managed
APEX is enabled in the database tools configuration. Provisioning is an OCI
administrator action, not an installer phase.

Use an Autonomous Database capable of the bundle's SQL property graphs and
native JSON objects, with managed APEX available. The supplied application requires
compatibility with **APEX 26.1.5**; do not assume it imports into older versions. Record
actual database, APEX and SQLcl versions in your private deployment record.
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

### Example: reporting and inventory policy

Create this policy in your tenancy's root compartment. Replace
`MetisCloudDatabase` with the dynamic group that selects your database, qualified
with its identity domain where required. A single database needs only its own
dynamic group; repeat the grants for another group only if another approved
resource also needs this access.

```text
define tenancy usage-report as ocid1.tenancy.oc1..aaaaaaaaned4fkpkisbwjlr56u7cj63lf3wffbilvqknstgtvzub7vhqkggq
endorse dynamic-group MetisCloudDatabase to read objects in tenancy usage-report
Allow dynamic-group MetisCloudDatabase to inspect tenancies in tenancy
Allow dynamic-group MetisCloudDatabase to read all-resources in tenancy
```

Keep the `define tenancy` identifier exactly as shown for the Oracle report
store described in the [Required IAM Policy](https://docs.oracle.com/en-us/iaas/Content/Billing/Concepts/costusagereportsoverview.htm#policy).
It is Oracle's published tenancy identifier, not a customer identifier or secret.
Do not replace it with your own tenancy OCID. Check the applicable Oracle
instructions if deploying in a different realm.

Oracle's example uses `endorse group` for a human-user group. The example above
uses `endorse dynamic-group` because MetisCloud calls OCI through its database
Resource Principal. Oracle also documents this form in its
[cost-report access example](https://docs.oracle.com/en-us/iaas/Content/Functions/Tasks/functions_pbf_catalog_cost_reports_focus_converter.htm)
under Permissions.

The endorsement permits reads from Oracle's report store. The two `Allow`
statements permit tenancy inspection and resource reads in your tenancy.
`read all-resources in tenancy` gives the database broad read access, including
data exposed by services' read permissions. Use it when the approved reporting
scope covers the tenancy. For a restricted deployment, have the OCI
administrator replace it with the required service and compartment grants.
It does not authorize creating, changing or deleting resources.
Oracle's [IAM verb reference](https://docs.oracle.com/en-us/iaas/Content/Identity/policieshow/Verbs.htm)
explains the access included in `inspect` and `read`.

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

Use the reporting policy above for the core read-access example. Add permissions
for optional AI, email or operational actions when enabling those features.
Keep the approved policy with your private deployment record and verify access
through Setup's connectivity checks and the relevant feature.

## Workstation and saved connection

### Install the latest SQLcl

Download the latest release from
[Oracle SQLcl Downloads](https://www.oracle.com/database/sqldeveloper/technologies/sqlcl/download/).
Use Oracle's current release rather than a SQLcl copy bundled with an older tool.
Oracle's [installation instructions](https://docs.oracle.com/en/database/oracle/apex/26.1/aeadm/downloading-and-installing-sqlcl.html)
describe the ZIP installation and Java requirements.

1. Install Java 21, or another Java version supported by the SQLcl release you
   download. A package that includes a Java runtime can use its bundled runtime.
2. Download and extract the SQLcl ZIP into a tools directory outside the MetisCloud
   distribution. The extracted `sqlcl/bin` directory contains `sql` on macOS/Linux
   and `sql.exe` on Windows.
3. Add that `bin` directory to your shell's `PATH`. For macOS/Linux or a compatible
   Bash environment, replace the example path below:

   ```bash
   export PATH="/path/to/sqlcl/bin:$PATH"
   sql -version
   ```

4. Confirm that the reported release matches the one you installed. Add the same
   PATH setting to your shell startup configuration if you want it available in
   future terminals. If an older release still starts, check `command -v sql` and
   correct the PATH order.

For Windows, install SQLcl and Java in a configuration accessible from the Bash
environment that will run the deployer. A SQLcl installation available only to a
different shell or operating environment will not satisfy the deployer's checks.

### Check the workstation tools

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

### Save and test the ADMIN connection

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

Continue with [Installation](installation.md), then [First use](first-use.md).
