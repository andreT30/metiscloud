# First sign-in and setup

Start here after [installation](installation.md). Allow time for the first data
load: the most recent 15 days load first, while older history can take hours.

## Sign in as the first administrator

Open the installed application using the target URL reported by your environment.
Use the first MetisCloud administrator established during INITIAL. Database
ADMIN and workspace administrator credentials are not interchangeable with this
application account.

Confirm that Administration opens. If it does not, check the installed
authentication scheme and role membership with the workspace administrator.
Do not disable authorization to get past an access error.

For Oracle APEX Accounts, manage accounts, passwords and role groups in APEX
workspace administration. The default groups are `Administrators`,
`Contributors` and `Users`. SSO requires its own identity-provider setup;
see [Authentication](configuration.md#authentication).

## Complete Setup

Open **Administration → App Config → Setup**. Keep optional AI and email features off until
you have approved their access and data handling.

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
[optional AI setup steps](configuration.md#optional-ai-features) before applying
the draft. Otherwise leave them off and complete the data-load checks first.

A progress display of 100% with **Final processing** is still running. If history
fails, nightly cost ingestion remains on hold. Inspect the failed step and use
the supported retry after fixing its cause. A stale RUNNING record needs execution
verification before reconciliation; see [Troubleshooting](troubleshooting.md).

## Check one useful result

1. Open Home and select the intended currency and billing account.
2. In Analysis, choose a completed period covered by the successful load.
   Compare one known service/compartment total with the source reports.
3. In OCI Tools, find a known resource and check its region, compartment and tags.
4. Create a workload in Administration and verify its membership and cost.
5. Test an ordinary user's allowed and denied workloads using a separate session.

An empty result can mean that history is still loading. An authorization error
requires an access check, not another ingestion job.

## Give other users access

For the role matrix and provider-specific steps, see
[Users, groups and workload access](access-control.md).

In **Administration → App Config → User access**, inspect Role groups and manage each user's
local access. Administrators see all workloads and administration. Contributors
see all workloads outside Administration. Users see only individually assigned
workloads. Administrator takes precedence over Contributor, then User.

For APEX accounts, create the account and group membership in the workspace,
then use **Link existing account** to prepare assignments before first sign-in.
For SSO, accounts normally appear after verified sign-in. **Preassign SSO
workloads** accepts the exact case-sensitive username; pending assignments grant
no access and activate only when that identity first signs in with the User role.
If its role differs, an administrator must review the assignments later.

Choose **Manage**, select the allowed workloads and **Save assignments**. A User
with none active sees **Awaiting workload assignment**, rather than unrestricted
data. After assignment activation, they can refresh that page to open Analysis.
Test both allowed and denied access. Group membership alone must not give a User
all workloads.

Disabling local access blocks this application without deleting the APEX or OCI
account. The initial administrator is protected and at least one administrator
must remain enabled. Role changes require a new sign-in; review stale assignments
after a role change. Never reuse an SSO username for a different person.

## Enable recurring work

Only after a successful initial load, review and enable the intended schedules.
Retention, archive and expiry jobs need their own approval. Do not enable every
job as a shortcut. Assign someone to monitor the next run and respond to failures.

Use [Configuration](configuration.md) for settings, [Administration](administration.md)
for daily operations and [Usage](usage.md) for reports.
