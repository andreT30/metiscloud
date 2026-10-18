# Verify a deployment

Before admitting users, keep the bundle version and hash, database/APEX/SQLcl
versions, network mode, date and results in a private deployment record.
Keep endpoints and operational logs private.

## Installation and reporting

Verify the bundle checksum and review the installer result, compilation status
and scheduler state. Investigate failures before continuing with Setup.

Complete Setup and follow the first load and older-history collection to
completion. Confirm that scheduled loads resume as intended. Compare a known
report with its source totals using the same currency, scope, period and units.
Open a downloaded report to verify its contents. The
[first-use guide](first-use.md#check-one-useful-result) gives the initial reporting steps.

## User and network access

Test Administrator, Contributor, assigned and unassigned User access. Confirm
that disabled and unmapped identities cannot access the application. For OCI
SSO, repeat these checks with real SSO sessions and use new sessions after role
changes. Follow the [access-control guide](access-control.md).

Verify installer and browser connectivity, required outbound service access and
rejection of unauthorized access for the selected public or private endpoint.
See [network preparation](prerequisites.md#public-or-private-networking).

## Optional services

If email is enabled, verify approved recipients and that report links require
the intended sign-in and access permissions. For AI, test model connectivity and
inference separately. Responses must use approved datasets and respect user
scope and data-sharing rules. See [configuration](configuration.md).

## Maintenance and recovery

Before UPDATE, preview its plan, resolve conflicts and take a recovery checkpoint.
Afterwards, verify configuration retention and replaced definitions using the
[upgrade guide](upgrade.md).

Rehearse restoration in an isolated environment with controlled schedules.
Test cleanup or uninstall only in a disposable environment if you need them.
See [backup and recovery](backup-and-recovery.md).

Use [troubleshooting](troubleshooting.md) for failures and
[security responsibilities](security.md) for ongoing controls.
