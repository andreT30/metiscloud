# Security policy

## Report a security concern

Use [GitHub Issues](https://github.com/andreT30/metiscloud/issues) or
[GitLab Issues](https://gitlab.com/andreT30/metiscloud/-/issues) on the host where
you follow the project. If you suspect a vulnerability, open an issue asking for
private follow-up. Include only the affected version and a general description;
keep reproduction steps, exploit details and sensitive evidence out of the
public issue. The maintainer can arrange a private reporting channel.

Please do not open a public issue with vulnerability details or a working exploit.

When a private reporting channel is available, include:

- The affected MetisCloud version and component.
- Steps to reproduce the issue in an authorized test environment.
- The access required to trigger it and the impact you observed.
- A minimal example with sensitive values removed.

Do not send passwords, tokens, wallets, customer data or unredacted operational
logs. If sensitive evidence is necessary, describe what it contains first so
a suitable way to share it can be agreed.

## What happens next

The maintainer will assess the report, discuss any missing reproduction details
with you and coordinate a correction and disclosure where appropriate. Please
allow time to investigate before publishing details that could expose users.
This community project does not guarantee response or remediation deadlines.

## Supported versions

Security corrections target the latest published MetisCloud release. Older
versions may not receive fixes; plan to update when a corrected release is
available. Check [release notes](CHANGELOG.md) and follow the
[upgrade guide](docs/upgrade.md).

## Secure your installation

Keep database and APEX access restricted, review application roles and workload
assignments, and grant only the OCI permissions needed by enabled features.
Protect backups and credentials, and review what data optional AI services receive.

See the [deployment security guide](docs/security.md) and
[user access guide](docs/access-control.md) for configuration responsibilities.

For ordinary bugs without a security impact, use
[GitHub Issues](https://github.com/andreT30/metiscloud/issues) or
[GitLab Issues](https://gitlab.com/andreT30/metiscloud/-/issues).
