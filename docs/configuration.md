# Configuration

Use **Administration → App Config → Setup** for initial environment settings and
**Application settings** for permitted edits to existing keys. Setup-managed
values can be edited there when they are not protected; the editor validates
their field values. Protected settings, including `ENDPOINT` and
`FIRST_SETUP_RUN`, cannot be changed there. There is no
customer action to add arbitrary keys. Do not bypass these controls with SQL.

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
See [First use](first-use.md) for Setup and [Administration](administration.md)
for workload, recipient and schedule management.

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
workload permissions are individual assignments; see [User access](first-use.md#give-other-users-access).
Passing native-account tests does not prove SSO parity.

## OCI access

The database Resource Principal supplies OCI credentials; IAM determines its
permissions. Use a dynamic group scoped to the intended database and grant only
the services/compartments needed. Network reachability and IAM are separate
checks. Follow [Environment preparation](prerequisites.md).

Do not paste OCI keys, tokens, passwords or SQLcl connection data into application
settings. A project identifier such as `OPENAI_PROJECT_ID` is private
configuration, not a replacement for service authorization.

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
