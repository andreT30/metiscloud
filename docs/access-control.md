# Users, groups and workload access

Sign-in establishes who a person is. MetisCloud role mappings and individual
workload assignments determine what that person can use and see. OCI IAM
separately controls what the database can collect or call.

## Know which administrator you need

| Role outside or inside the application | Responsibility |
| --- | --- |
| OCI administrator | Cloud resources, dynamic groups, IAM policies and network access. |
| Database ADMIN / DBA | Database privileges, prerequisites, recovery and installation decisions. |
| APEX workspace administrator | Workspace accounts, groups and authentication configuration. |
| MetisCloud Administrator | Application settings, workloads, local user access, schedules and administrative tools. |

Being a database or workspace administrator does not automatically give a person
the MetisCloud Administrator role. Use the first application administrator
established during INITIAL to complete Setup and configure access.

## Application roles

Inspect **Administration → App Config → User access → Role groups** for the
installed mappings. Default APEX account groups are case-sensitive:
`Administrators`, `Contributors` and `Users`.

| Application role | Workload scope | Administrative access |
| --- | --- | --- |
| Administrator | All workloads | Application administration and protected tools. |
| Contributor | All workloads on permitted non-administrative pages | No Administration access. |
| User | Individually assigned active workloads | No Administration access; only permitted workload-scoped pages. |

Administrator takes precedence over Contributor, then User. Membership in a broad
role therefore cannot be narrowed by adding a User workload assignment. Give
people the least privileged role that fits their work.

The role names do not mean every page is available to every role. For example,
Home and MetisCloud Chat are for Administrators and Contributors. OCI Tools
offers permitted workload-scoped views to Users, but Ratecard and Load Policies
require Administrator access. See the [page overview](pages-and-help.md).

## Add an Oracle APEX Accounts user

1. Have the APEX workspace administrator create the person's account and add it
   to the intended mapped group. Do not share administrator accounts.
2. In MetisCloud **User access**, choose **Link existing account** when preparing
   assignments before the user's first sign-in.
3. For a User, choose **Manage**, select the permitted workloads and
   **Save assignments**. Review their active state.
4. Test a fresh application session as that person. Check an allowed workload,
   a denied workload and an administrative page they must not access.

A User with no active assignments sees **Awaiting workload assignment**.
After assignments are activated, the user can refresh that page to open Analysis.
Adding the person to `Users` alone does not grant all-workload access.

## Use OCI SSO

Have the identity and APEX administrators configure the provider, web credential,
callback/logout URLs, claims and role-group mappings before switching schemes.
Setting a discovery URL alone is insufficient. See
[authentication configuration](configuration.md#authentication).

SSO accounts normally appear after verified sign-in. To prepare workload access
in advance, use **Preassign SSO workloads** with the exact case-sensitive username.
Pending assignments grant no access. They activate when that identity first signs
in with the User role; a different role requires administrator review later.

Never reuse an SSO username for a different person. Validate administrator,
Contributor, assigned User, unassigned User and disabled-user behavior with real
SSO sessions. A successful native-account test does not establish SSO parity.

## Change or revoke access

Disable local access in **User access** to block this application without deleting
the person's APEX or OCI identity. The initial administrator is protected and at
least one administrator must remain enabled. Coordinate account-level revocation
and session handling with the identity administrator when needed.

Role changes require a new sign-in. Review existing assignments after a role
change and repeat allowed/denied tests. Changing report filters, creating a saved
report or naming a workload does not grant authorization.

Recheck mappings after an authentication change or upgrade. Keep access reviews
and operational evidence private. For failures, use
[access troubleshooting](troubleshooting.md#sign-in-or-workload-access-fails);
do not disable authorization to make a page open.
