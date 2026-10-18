# Install MetisCloud

Use this guide to prepare the database and workstation, run INITIAL and open the
installed application. Then continue with [Set up and configure MetisCloud](configuration.md).

MetisCloud runs in Oracle Autonomous Database with managed APEX. We strongly
recommend **Autonomous AI Lakehouse**, formerly Autonomous Data Warehouse, for
its batch loads and analytical queries. Users open the application in a browser;
the database stores application data and runs collection jobs. No separate
MetisCloud application server is needed.

Public and private endpoints use the same bundle and installer. Self-managed
Database, APEX and ORDS are outside this guide. The installer does not provision
cloud infrastructure. Do not run INITIAL over an existing application schema;
use [Upgrade](upgrade.md) for an installed environment.

## In this guide

- [People and access](#people-and-access)
- [Example names](#names-used-in-the-installation-examples)
- [Database compatibility](#database-compatibility)
- [Prepare an Autonomous Database](#prepare-an-autonomous-database)
- [Public or private networking](#public-or-private-networking)
- [Resource Principal and IAM](#resource-principal-and-iam)
- [Workstation and saved connection](#workstation-and-saved-connection)
- [Get and verify the distribution](#get-the-distribution)
- [Database and workspace settings](#choose-database-and-workspace-settings)
- [Installation journal](#prepare-the-installation-journal)
- [Run INITIAL](#run-the-guided-installer)
- [Check the result](#check-the-result)

## People and access

| Owner | Prepare |
| --- | --- |
| OCI administrator | Deployment compartment, database, approved data scope, dynamic group, service-specific IAM policies and network access. |
| DBA | ADMIN access, database capabilities, automatic indexing, recovery point and maintenance window. |
| APEX workspace administrator | Workspace access, authentication and application user/group setup. This is separate from database ADMIN. |
| Application administrator | Reporting scope, regions, tags, first-load period, recipients and job approval. |

Keep actual OCIDs, endpoints, connection details and approvals in a private
deployment record outside this repository.

## Names used in the installation examples

These are generic names, not required values or a reference environment.
Replace them consistently throughout the procedure. Values in angle brackets,
such as `<ADB_OCID>`, must also be replaced before use.

| Example | Meaning |
| --- | --- |
| `TARGET_ADMIN` | SQLcl saved connection for database ADMIN. |
| `MC_TARGET` | Unused database schema name and the target SQLcl saved-connection name. |
| `MC_WORKSPACE` | APEX workspace to create, or an approved existing workspace. |
| `WORKSPACE_ADMIN` | Workspace administrator identity; its requirements depend on Builder authentication. |
| `MC_ADMIN` | First MetisCloud application administrator. |
| `2300` | Application ID; confirm that it is unused in the target APEX instance. |
| `/path/to/private/metiscloud/initial-001` | New persistent directory where INITIAL records changes for failed-install recovery. The installer creates `initial-001`; its parent must already exist. See [installation journal](#prepare-the-installation-journal). |

## Database compatibility

Use an Autonomous Database capable of the bundle's SQL property graphs and
native JSON objects, with managed APEX available. The supplied application requires
compatibility with **APEX 26.1.5**; do not assume it imports into older versions. Record
actual database, APEX and SQLcl versions in your private deployment record.
Do not infer compatibility with other database or APEX versions from a successful
connection alone.

Database-wide automatic indexing and Resource Principal settings require DBA
approval. Review the [standalone installer](../db/deployer/install_mc_bundle.sh),
including property-graph and context privileges. Do not assume a restricted or
free service tier meets these requirements.

Choose an unused schema name, workspace name and non-conflicting application ID.
INITIAL creates the schema; do not create an empty schema in advance.
The examples use `MC_TARGET`, `MC_WORKSPACE` and app ID `2300`; these are examples,
not required identities.

## Prepare an Autonomous Database

An OCI administrator completes these steps before installation. Use Oracle's
[database provisioning guide](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/autonomous-provision.html)
for the current Console screens.

1. Select the deployment region and compartment. Keep the database OCID and
   compartment OCID in your private deployment record; they identify different
   resources and are not interchangeable.
2. Select the **Lakehouse** workload type and create the database with managed
   APEX and the capabilities in
   [database compatibility](#database-compatibility). Choose
   compute, storage, autoscaling and backup settings for your expected workload.
3. Select restricted public access or a private endpoint. For public access,
   allow the approved installer and browser networks. For private access, select
   the approved VCN/subnet and arrange routing and DNS for both clients.
4. Set the database ADMIN password through the Console and store it in your
   approved password manager. Do not put it in a command or deployment document.
5. Wait for the database to become available. Open its managed APEX service and
   confirm that the sign-in page is reachable from the intended user network.
6. Prepare the [Resource Principal policies](#resource-principal-and-iam) and
   [SQLcl connection](#save-and-test-the-admin-connection).
   The installer creates the application schema later; do not create it manually.

Before proceeding, you should have an available database, a reachable APEX
sign-in page and an approved network path for SQLcl. An OCI Console login alone
does not confirm any of these application access paths.

### Capacity, cost and recovery

Choose compute, storage, autoscaling and backup retention with the DBA. Data
volume depends on tenancy size, reporting history, collected resource types and
enabled features. This guide does not prescribe a universal minimum sizing profile.

Budget for the cloud services you enable, including database operation and
optional AI or email usage. Application licensing does not cover service charges.
Avoid assuming a free or restricted service tier satisfies the database
capabilities listed in [database compatibility](#database-compatibility).

Decide who owns recovery and rehearse a restore before production reliance.
The bundle is not a backup of your data, IAM policies or identity-provider settings.
Follow [backup and recovery](backup-and-recovery.md).

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
Verify access from an allowed network and confirm that the configured network
policy rejects unauthorized access before admitting users.

A private database endpoint does not make all dependencies private. Browsers
also load external JavaScript and font assets, and optional AI features send
approved context to their configured service. Review the
[network and data-flow inventory](security.md#network-and-data-flow-inventory).

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


## Get the distribution

Open the [MetisCloud repository](https://github.com/andreT30/metiscloud), select
**Code → Download ZIP**, and extract the download into a working directory.
Alternatively, clone the repository if you already use Git. Git is not required
to run the installer.

Open a terminal in the extracted directory containing `README.md`. Check that
`bundle-app.zip`, `bundle-app.zip.sha256`, `VERSION` and
`db/deployer/install_mc_bundle.sh` are present. Keep the application bundle ZIP
intact; the installer reads it directly. Do not mix a bundle with a checksum or
installer from a different distribution.

Keep wallets, journals and connection records outside this directory. Retain
the supplied distribution and version for your installation record.

## Verify the supplied release

Run from the repository root:

```bash
shasum -a 256 -c bundle-app.zip.sha256
```

Use `sha256sum -c bundle-app.zip.sha256` where `shasum` is unavailable.
Stop if the checksum check fails. It checks the ZIP against the supplied checksum
without connecting to your database. Obtain both files from a trusted source;
a checksum alone does not authenticate the publisher. The installer performs
additional bundle validation before applying the release.

The expected checksum result is `bundle-app.zip: OK`. Resolve a missing file or
checksum mismatch before connecting the installer to a database.

## Choose database and workspace settings

The installer distinguishes a new dedicated database from an existing shared
database. Choose `NEW` only when you authorize database-wide prerequisite
configuration, including Resource Principal and automatic indexing settings.
Choose `EXISTING` for a shared database; missing database-wide prerequisites
require separate approval. Neither option creates the Autonomous Database.

Choose `CREATE` for a new APEX workspace or `EXISTING` for a workspace approved
to map the new schema. A workspace administrator and the first MetisCloud
administrator are separate accounts/roles. The installer detects workspace
builder authentication; do not enter an external identity-provider password.

## Prepare the installation journal

INITIAL writes a recovery journal on the computer running the installer. It
records the target identity, completed changes and recovery information so
CLEANUP can identify resources created by a failed installation. This is an
operational record, not a database backup.

Choose a persistent private directory outside the distribution, for example
`/path/to/private/metiscloud/initial-001`. Replace the whole example path with
a location owned by the installer operator. Create its parent directory first
and restrict access to that operator. Leave `initial-001` absent: the installer
creates it with owner-only permissions (`700`). Use a new name for each attempt
and retain the original journal if an attempt fails.

Do not use `/tmp`, `/var/tmp`, a disposable container filesystem or another
location subject to automatic cleanup. Recovery may be needed after a reboot
or in a later session. The path must be absolute, use letters, digits, `_`, `-`,
`.` and `/`, and contain no spaces, traversal components or symbolic links.
Keep the journal after installation according to your operational retention
policy and protect any retained copy. Use the original location for
[failed-install recovery](recovery-and-removal.md#clean-up-a-failed-initial).

## Run the guided installer

For a first installation, start guided INITIAL:

```bash
bash db/deployer/install_mc_bundle.sh --mode INITIAL --interactive
```

`--mode INITIAL` selects a new installation. `--interactive` asks for missing
installation settings. You can supply some settings as flags and let the guided
installer ask for the rest. Have these values ready:

- The path to `bundle-app.zip` and the ADMIN saved connection name.
- The new schema and its SQLcl saved-connection name.
- Database mode, workspace name/mode and workspace administrator where needed.
- Target application ID and APEX offset policy. Use `AUTO` for a new installation.
- The first MetisCloud administrator's username, password and email.
- A new absolute journal directory in private, persistent storage outside this repository.

Review the summary before confirming the exact schema name. Password prompts
occur during preparation, before target mutation. If replacing an existing saved
connection, review the prompt; its backup is retained until success or cleanup.
Protect the journal and terminal output as operational records.

If asked for an **Optional prerequisite SQL file**, leave it blank. The current
installer does not accept custom prerequisite scripts; a DBA must run any
approved custom preparation separately before INITIAL.

### Supply settings on the command line

Use this form when you already know the installation settings. It still prompts
for passwords, the application administrator's contact address, and any required
Builder account details. It is not an unattended installation.

```bash
bash db/deployer/install_mc_bundle.sh \
  --mode INITIAL --bundle bundle-app.zip \
  --database-mode NEW \
  --admin-connection TARGET_ADMIN \
  --target-connection-name MC_TARGET \
  --schema MC_TARGET --confirm-create-schema MC_TARGET \
  --workspace MC_WORKSPACE --workspace-mode CREATE \
  --first-admin MC_ADMIN \
  --target-app-id 2300 --apex-offset AUTO \
  --journal-directory /path/to/private/metiscloud/initial-001
```

Replace every example name and the journal path with your approved values.
Use the [example-name reference](#names-used-in-the-installation-examples)
to distinguish the database, workspace and application accounts. The example
leaves the workspace administrator unspecified so the installer can prompt after
detecting Builder authentication. For Autonomous Database
sign-in, choose the new target schema account or an existing eligible database
account as instructed by the prompt. Do not invent a separate database account
name that does not exist.

For a shared database, use `EXISTING` and arrange prerequisite approval
with its DBA rather than copying the `NEW` choice above.
INITIAL `--dry-run` previews the phases without creating the schema or importing
APEX; it cannot prove OCI access or database compilation.

### INITIAL command options

The following settings must be supplied on the command line when you omit
`--interactive`. In guided mode, missing values are requested at prompts.
The journal is required for a real installation, but may be omitted for a preview.

| Option | Meaning |
| --- | --- |
| `--mode INITIAL` | Select a new installation. Use UPDATE only for an already installed target. |
| `--bundle FILE` | Path to the supplied `bundle-app.zip`. Keep it zipped. |
| `--admin-connection NAME` | Existing SQLcl saved connection for database ADMIN, such as `TARGET_ADMIN`. |
| `--target-connection-name NAME` | Saved connection the installer creates for the new application schema, such as `MC_TARGET`. It must differ from the ADMIN connection name. If that name already exists, the installer asks before replacing it. |
| `--schema OWNER` | New application schema to create. It must not already exist. |
| `--confirm-create-schema OWNER` | Repeat the schema name exactly to confirm creation. This is a confirmation, not a second schema. |
| `--workspace NAME` | APEX workspace name to create or reuse. |
| `--workspace-mode CREATE\|EXISTING` | `CREATE` creates a new workspace; `EXISTING` maps the new schema to an existing workspace and preserves its administrator accounts. |
| `--first-admin USERNAME` | New local APEX end-user account that becomes the first MetisCloud Administrator. This is separate from the workspace Builder administrator. |
| `--apex-offset AUTO\|NUMBER` | Component-ID offset for the APEX import. Use `AUTO` for a new installation. A fixed non-negative number is for a deliberately planned import; this is separate from the application ID. |
| `--journal-directory PATH` | New absolute private directory for persistent recovery records. Its parent must exist. See [journal preparation](#prepare-the-installation-journal). |

The remaining options control the installation choices or supply advanced settings:

| Option | Default or behavior when omitted |
| --- | --- |
| `--interactive` | Without it, required installation settings above must be provided as flags. Password and account-detail prompts still occur during a real run. |
| `--database-mode NEW\|EXISTING` | A real run asks when omitted and offers `EXISTING` as the default. A preview defaults to `EXISTING`. Choose `NEW` only to authorize database-wide prerequisite configuration on a new dedicated database. This option does not provision a database. |
| `--target-app-id ID` | Defaults to the application ID recorded in the bundle. Specify an unused positive ID explicitly, such as `2300`, to control the installed identity. |
| `--workspace-admin USERNAME` | Prompted when creating a workspace. The identity must suit the detected Builder authentication. Use only with `--workspace-mode CREATE`; it must differ from `--first-admin`. |
| `--default-tablespace NAME` | Defaults to `DATA`. The permanent tablespace must already exist and be online. |
| `--temporary-tablespace NAME` | Defaults to `TEMP`. The temporary tablespace must already exist and be online. |
| `--dry-run` | Preview the installation phases. Omit it to install. A preview does not create the target, validate live connectivity or prove successful compilation. |
| `--help` | Print the command summary and exit. |

Append `--dry-run` to the parameterized example to preview those settings. After
review, run the same command without `--dry-run` to install. Keep the same
chosen identities and use a new journal directory for the real attempt.

Use `--admin-connection` and `--target-connection-name` for INITIAL;
`--connection` selects the existing target for UPDATE. INITIAL applies its
required schema grants automatically, so `--apply-missing-grants` is unnecessary.
Although the command summary lists `--environment-prerequisites`, the current
INITIAL path rejects custom prerequisite SQL files; leave that option unset.

## Check the result

Read the final summary and review compilation warnings before admitting users.
Confirm the target application ID, workspace and first administrator. Keep the
bundle version and hash, database/APEX/SQLcl versions, network mode and result
in a private deployment record. Application jobs start disabled; the install recovery monitor is an internal exception.

Ask the workspace administrator to open App Builder in the target workspace and
confirm that the installed application is present with the chosen ID. Run it to
obtain the application sign-in URL. Keep that URL in your private deployment
record and use it for [first sign-in](configuration.md#sign-in-as-the-first-administrator).

Keep the journal after success under your operational retention policy. If
installation fails, preserve it and follow [failed-install cleanup](recovery-and-removal.md).
Do not repeatedly run INITIAL against a partially created schema.

Continue with [Set up and configure MetisCloud](configuration.md) to configure OCI
access, load data and grant user access.
