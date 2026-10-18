# Set up and configure MetisCloud

Start after [installation](installation.md), using the first application
administrator created by INITIAL. Complete Setup, verify the first load and
configure user access before enabling recurring work. The most recent 15 days
load first; older cost history can take hours.

Keep actual identifiers, endpoints and verification results in a private
deployment record. Use the **?** button or page-help widget for field details.

## In this guide

- [First administrator sign-in](#sign-in-as-the-first-administrator)
- [Complete Setup](#complete-setup)
- [Verify the first load](#verify-the-first-load)
- [Give other users access](#give-other-users-access)
- [Enable recurring work](#enable-recurring-work)
- [Optional AI features](#optional-ai-features)
- [Optional Email Delivery](#email-delivery-and-exports-optional)
- [Change application settings](#change-application-settings)
- [Target settings reference](#target-settings)
- [Authentication](#authentication)
- [OCI access](#oci-access)

## Sign in as the first administrator

Open the application URL supplied by the workspace administrator. If you do not
have it, open managed APEX from the database's tools in the OCI Console, sign in
to the target workspace as a Builder user, and open App Builder. Select the
installed application by its ID and run it. Bookmark the application URL, not
the App Builder URL. On a private endpoint, use the approved connected network.

Use the first MetisCloud administrator established during INITIAL. Database
ADMIN and workspace administrator credentials are not interchangeable with this
application account.

Confirm that Administration opens. If it does not, check the installed
authentication scheme and role membership with the workspace administrator.
Do not disable authorization to get past an access error.

For Oracle APEX Accounts, manage accounts, passwords and role groups in APEX
workspace administration. The default groups are `Administrators`,
`Contributors` and `Users`. SSO requires its own identity-provider setup;
see [Authentication](#authentication).

## Complete Setup

Open **Administration → App Config → Setup**. Keep optional AI and email features off until
you have approved their access and data handling.

Have these values ready before applying the configuration:

| Value | Where to obtain it or how to choose it |
| --- | --- |
| Root tenancy identifier | Copy your tenancy OCID from OCI tenancy details. This is different from Oracle's report-store tenancy identifier in the IAM policy. |
| Database region | Use the region containing the installed Autonomous Database. |
| Home region | Check your tenancy's home region; it may differ from the database region. |
| Cost report location | Confirm the location of your tenancy's FOCUS reports using OCI Cost and Usage Reports and the field's in-app help. Do not substitute an arbitrary bucket. |
| Defined tags | Use your existing namespace and key names. For example, `Finance.CostCenter` means namespace `Finance`, key `CostCenter`; use it only if that tag exists in your tenancy. |
| Collection regions | Choose subscribed regions within your approved reporting scope. |

The [configuration reference](#target-settings) maps these choices to their
application settings.

1. Enter the target tenancy, regions, cost report
   location and tagging choices. Review the descriptions alongside each field.
2. Save/apply the draft. Editing a profile later invalidates earlier connectivity
   evidence, so repeat the checks after a change.
3. Run the connectivity checks. Resolve required failures before starting a load.
   PASS confirms the named check. NOT_TESTED is not a pass: for example, no
   representative report file may have been available. SKIPPED means the check
   did not run. These checks do not invoke an AI model.
4. Deliberately start the initial load once. Do not submit again because the
   browser is slow. Record the run ID and follow Load Status.
5. Wait for the recent 15-day load to finish. The application can become ready
   while older cost history continues in the background. Nightly cost ingestion
   waits for that history to complete.
6. Review the results and finish Setup. The Setup area then closes; Load Status
   remains available while outstanding work needs attention.

Setup initially configures the root tenancy. Add child-tenancy scope only after
this path succeeds and IAM has been reviewed. Availability history accepts 1 to
89 days. A planned SSO choice in Setup does not switch authentication for you.

If you are enabling the Bot and Widget during Setup, follow the
[optional AI setup steps](#optional-ai-features) before applying
the draft. Otherwise leave them off and complete the data-load checks first.

A progress display of 100% with **Final processing** is still running. If history
fails, nightly cost ingestion remains on hold. Inspect the failed step and use
the supported retry after fixing its cause. A stale RUNNING record needs execution
verification before reconciliation; see [Troubleshooting](troubleshooting.md).

## Verify the first load

Open Home and select the intended currency and billing account. In Analysis,
choose a completed period covered by the successful load and compare one known
service or compartment total with the source reports. Use the same period,
currency, scope and units, and open a downloaded report to check its contents.
In OCI Tools, find a known resource and confirm its region, compartment and tags.

Create a workload in Administration and verify its membership and cost. The
[administration guide](administration.md#workloads-and-budgets) covers workload management.
The [user guide](usage.md) explains reporting filters and comparisons.

Before inviting users, confirm that the recent load has completed and the report
and inventory checks above agree with their sources. Continue monitoring older
history until it finishes, then confirm scheduled loads resume as intended.
An empty result can mean that history is still loading. An authorization error
requires an access check, not another ingestion job.

## Give other users access

Open **Administration → App Config → User access** and inspect **Role groups**.
Use [Users, groups and workload access](access-control.md) for the role matrix,
account creation, workload assignment and revocation procedures. A workspace or
database administrator is not automatically a MetisCloud Administrator.

For a first restricted-user test, create a separate APEX account named
`MC_READER` with membership in `Users` only. In User access, choose
**Link existing account**, then **Manage**. Select one permitted workload and
**Save assignments**. Review its active state.

Sign in as `MC_READER` in a separate browser session. The assigned workload must
be visible, an unassigned workload must be unavailable, and Administration must
be denied. Use your own account name and workloads. A User with no active
assignments sees **Awaiting workload assignment** and can refresh that page
after assignments activate.

Before wider use, test Administrator, Contributor, assigned User and unassigned
User behavior. Confirm that disabled and unmapped identities cannot access the
application. Role changes require a new sign-in. If using OCI SSO, complete
[authentication configuration](#authentication) and repeat these checks with
real SSO sessions. Review existing assignments after a role change.

## Enable recurring work

Only after a successful initial load, review and enable the intended schedules.
Retention, archive and expiry jobs need their own approval. Do not enable every
job as a shortcut. Assign someone to monitor the next run and investigate failures
before relying on its data.
Use [Administration](administration.md) for schedule and retention management.

## Optional AI features

Confirm the organization permits questions, context and results to reach the
selected OCI Generative AI service before enabling use.

In **Administration → App Config → Setup**, open **Optional features**:

1. Set **Prepare Bot and Widget** to **Yes**.
2. Enter the target AI compartment and up to three AI regions, separated by commas.
3. Enter an existing OCI GenAI project identifier in the region of the model you
   intend to use. Setup does not create the project or grant OCI permissions.
4. Select **Fetch OCI models**, then choose a model by name and region.
5. Save the draft. Under **Review and load**, select **Apply configuration**.
6. Run **Check connectivity** and review the Bot configuration results.

Fetching models uses the database Resource Principal. It lists active chat
models without saving configuration, sending a prompt or enabling schedules.
If any selected region fails, correct its access or remove it and fetch again.
Your draft remains available. Changing the compartment or regions clears the
model selection. Reduce the region list if a discovery limit is reached.

Apply checks the selection again and saves the default model, its region-derived
endpoint and the project for the default Bot and Widget (`LATEST / PROD`).
Existing model tuning is retained. Review any other dataset/environment overrides
flagged by the checks in [Assistant administration](assistant-administration.md).

Use Setup for these coordinated changes. Editing an individual value in
Application settings does not perform the complete model/project setup.
Repeat checks after edits. Review and apply a saved Setup configuration from an
earlier release again before relying on its old readiness result.

A passing configuration or model-discovery check does not prove inference
access. Send an approved test question separately. The AI model discovery
schedule is managed separately from Setup.

Use [Assistant administration](assistant-administration.md) to configure
parameters, approved datasets and file search. Test a known question and an
out-of-scope request with the intended user's access before wider use.

## Email Delivery and exports (optional)

In **Administration → App Config → Email Delivery**:

1. Enter the approved-sender compartment, OCI region, API realm domain and
   approved sender. Set the target HTTPS APEX gateway base URL through `/ords`.
2. Use **Refresh endpoint** to discover the submission endpoint. Do not enter
   SMTP passwords or reuse another environment's endpoint.
3. Add enabled Recipient Accounts mapping application principals to addresses.
   Mark approved operators as **Operations recipient** for system notifications.
4. Use **Send Test** for an approved recipient and inspect Email Queue details.
5. Test a requested report-download link as the intended user. It must require
   sign-in and point to this installed application.

System mail uses enabled Operations recipients only. Requested report files use
the current user's enabled mapping, or a valid email-form SSO username fallback.
Missing target app/workspace configuration stops background delivery.

Email Templates supports subject, text and optional HTML changes. Use only
listed placeholders; saving creates an audited version and dynamic HTML values
are escaped. See [queue handling](administration.md#email-queue) before retrying
a failed or held message.

## Change application settings

Use **Administration → App Config → Setup** for initial environment settings and
**Application settings** for permitted edits to existing keys. Setup-managed
values can be edited there when they are not protected; the editor validates
their field values. Protected settings, including `ENDPOINT` and
`FIRST_SETUP_RUN`, cannot be changed there. There is no
customer action to add arbitrary keys. Do not bypass these controls with SQL.

Before a significant configuration change, arrange a recoverable schema backup
with the DBA; see [Backup and recovery](backup-and-recovery.md).

## Target settings

Keep actual identifiers and endpoints in your private deployment record.
`APP_CONFIG` contains runtime configuration, not passwords or wallet contents.

| Setting | What to supply or verify |
| --- | --- |
| `OCI_ROOT_COMPARTMENT_OCID` | Intended root tenancy identifier. |
| `COMPARTMENT_ID` | AI compartment used for model configuration. |
| `FC_ADB_REGION`, `OCI_HOME_REGION` | Database region and tenancy home region, which may differ. |
| `OBJECT_BASE_URI` | Your cost report objects location, checked against available reports. |
| `COST_TAG_COSTCENTER_KEY` | Defined tag in namespace.key form used for workload attribution. |
| `COST_TAG_ENVIRONMENT_KEY` | Defined tag used to identify environments. |
| `COST_TAG_CREATED_BY_KEY`, `COST_TAG_IMPLEMENTOR_KEY` | Corresponding tagging conventions where used. |
| `AVAILABILITY_REGIONS` | Subscribed regions to collect; review each region's outcome. |
| `AI_MODEL_REGIONS` | Approved model-discovery regions for optional AI features. |

The installer establishes `APEX_APP_ID`, `APEX_APP_OFFSET`,
`APEX_WORKSPACE_NAME` and `l_auth_scheme_name`. These describe the installed
identity and authentication choice. A wrong ID or offset can break an update.
Do not change processing table names, procedure names or parsing defaults to
repair an unrelated ingestion problem.

Save a focused change, rerun the relevant checks and keep affected schedules
disabled on failure. Profile changes invalidate earlier connectivity evidence.
See [Administration](administration.md) for workload, recipient and schedule management.

## Authentication

Oracle APEX Accounts uses workspace users and the configured role groups.
An APEX workspace administrator is not automatically a MetisCloud administrator.
Inspect Role groups in User access and test a fresh application session.

For OCI SSO, an identity administrator must configure the target APEX web
credential, discovery endpoint, callback/logout URLs, claims and role-group
mapping before switching schemes. `OIDC_DISCOVERY_URL` alone does not provision
an identity provider. Keep the credential in its supported APEX credential store,
not in APP_CONFIG or this repository.

Confirm the current authentication scheme and persisted target setting agree.
Test administrator, Contributor, assigned User, unassigned User and disabled
local-user access. Recheck those cases after upgrades and role changes. User
workload permissions are individual assignments; see [User access](#give-other-users-access).
Passing native-account tests does not prove SSO parity.

## OCI access

The database Resource Principal supplies OCI credentials; IAM determines its
permissions. Use a dynamic group scoped to the intended database and grant only
the services/compartments needed. Network reachability and IAM are separate
checks. Follow [Resource Principal and IAM](installation.md#resource-principal-and-iam).

Do not paste OCI keys, tokens, passwords or SQLcl connection data into application
settings. A project identifier such as `OPENAI_PROJECT_ID` is private
configuration, not a replacement for service authorization.

Continue with [Usage](usage.md) for reports, [Administration](administration.md)
for routine operation and [Troubleshooting](troubleshooting.md) for failures.
