# Infrastructure overview

MetisCloud runs in your Oracle Autonomous Database with managed Oracle APEX.
For new installations, strongly prefer the **Autonomous AI Lakehouse** workload
type, formerly Autonomous Data Warehouse. MetisCloud loads data in batches and
runs analytical queries, making Lakehouse the recommended choice. Oracle's
[workload types guide](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/about-autonomous-database-workloads.html)
describes the available choices.

Users open the application in a browser. The application schema stores collected
data and configuration; database jobs collect reports and inventory from OCI.
You do not need a separate MetisCloud application server for this deployment path.

## Components to prepare

| Component | Purpose | Who prepares it |
| --- | --- | --- |
| Autonomous Database and managed APEX | Host the database objects and browser application. | OCI administrator and DBA |
| Application schema and APEX workspace | Keep the installation's objects, application identity and workspace access. INITIAL creates or associates these using your selected settings. | Installer operator and APEX administrator |
| Database Resource Principal and OCI IAM | Authorize database calls to approved OCI services and compartments. | OCI administrator and DBA |
| Cost reports, inventory and Monitoring access | Supply the reporting and availability data for the approved tenancy scope. | OCI administrator |
| Operator workstation | Run the installer through Oracle SQLcl saved connections. | Installer operator |
| Browser access | Reach managed APEX and required browser assets. | Network and identity administrators |
| Generative AI and Email Delivery | Support optional assistant and email features. | Owners of those enabled services |

The public repository supplies the application bundle and target installer.
Provision the database, networks and OCI policies before running INITIAL.
No infrastructure-provisioning stack is included.

## Prepare an Autonomous Database

An OCI administrator completes these steps before installation. Use Oracle's
[database provisioning guide](https://docs.oracle.com/en-us/iaas/autonomous-database-serverless/doc/autonomous-provision.html)
for the current Console screens.

1. Select the deployment region and compartment. Keep the database OCID and
   compartment OCID in your private deployment record; they identify different
   resources and are not interchangeable.
2. Select the **Lakehouse** workload type and create the database with managed
   APEX and the capabilities in
   [database compatibility](prerequisites.md#database-compatibility). Choose
   compute, storage, autoscaling and backup settings for your expected workload.
3. Select restricted public access or a private endpoint. For public access,
   allow the approved installer and browser networks. For private access, select
   the approved VCN/subnet and arrange routing and DNS for both clients.
4. Set the database ADMIN password through the Console and store it in your
   approved password manager. Do not put it in a command or deployment document.
5. Wait for the database to become available. Open its managed APEX service and
   confirm that the sign-in page is reachable from the intended user network.
6. Prepare the [Resource Principal policies and SQLcl connection](prerequisites.md).
   The installer creates the application schema later; do not create it manually.

Before proceeding, you should have an available database, a reachable APEX
sign-in page and an approved network path for SQLcl. An OCI Console login alone
does not confirm any of these application access paths.

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
| `/path/to/private/metiscloud/initial-001` | New persistent directory where INITIAL records changes for failed-install recovery. The installer creates `initial-001`; its parent must already exist. See [installation journal](installation.md#prepare-the-installation-journal). |

## Check the three access paths

1. The operator's workstation must reach the database through the approved SQLcl
   connection. Protect any wallet and saved credentials outside the checkout.
2. Users' browsers must reach the managed APEX HTTPS endpoint. A working SQL
   connection alone does not prove that browser access works.
3. The database must reach the OCI services needed by enabled features and have
   permission to call them through its Resource Principal.

Public and private endpoint deployments use the same bundle. For public access,
restrict allowed networks according to your policy. For private access, arrange
private routing and DNS from both the installer host and user networks, plus
the necessary outbound service paths. See the
[network preparation](prerequisites.md#public-or-private-networking).

A private database endpoint does not make all dependencies private. Browsers
also load external JavaScript and font assets, and optional AI features send
approved context to their configured service. Review the
[network and data-flow inventory](security.md#network-and-data-flow-inventory).

## Capacity, cost and recovery

Choose compute, storage, autoscaling and backup retention with the DBA. Data
volume depends on tenancy size, reporting history, collected resource types and
enabled features. This guide does not prescribe a universal minimum sizing profile.

Budget for the cloud services you enable, including database operation and
optional AI or email usage. Application licensing does not cover service charges.
Avoid assuming a free or restricted service tier satisfies the database
capabilities listed in [requirements](prerequisites.md#database-compatibility).

Decide who owns recovery and rehearse a restore before production reliance.
The bundle is not a backup of your data, IAM policies or identity-provider settings.
Follow [backup and recovery](backup-and-recovery.md).

Continue with [environment preparation](prerequisites.md), then
[installation](installation.md).
