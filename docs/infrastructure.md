# Infrastructure overview

MetisCloud runs in your Oracle Autonomous Database with managed Oracle APEX.
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
[network preparation checklist](prerequisites.md#public-or-private-networking).

A private database endpoint does not make all dependencies private. Browsers
also load external JavaScript and font assets, and optional AI features send
approved context to their configured service. Review the
[network and data-flow inventory](security.md#network-and-data-flow-inventory).

## Capacity, cost and recovery

Choose compute, storage, autoscaling and backup retention with the DBA. Data
volume depends on tenancy size, reporting history, collected resource types and
enabled features. There is no established universal minimum sizing profile or
complete tested-version matrix for this candidate.

Budget for the cloud services you enable, including database operation and
optional AI or email usage. Application licensing does not cover service charges.
Avoid assuming a free or restricted service tier satisfies the database
capabilities listed in [requirements](prerequisites.md#database-compatibility).

Decide who owns recovery and rehearse a restore before production reliance.
The bundle is not a backup of your data, IAM policies or identity-provider settings.
Follow [backup and recovery](backup-and-recovery.md).

Continue with [environment preparation](prerequisites.md), then
[installation](installation.md).
