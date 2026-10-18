# Security responsibilities

MetisCloud spans the browser, Oracle APEX, the application schema, OCI services,
and optional AI services. Secure operation depends on controls at every boundary;
installing the bundle does not replace the operator's security responsibilities.

## Artifact trust

- Obtain `bundle-app.zip` and its sidecar from the same trusted release.
- Obtain the bundle and checksum from a trusted source and verify the SHA-256.
  The installer performs additional bundle checks before applying it. Checksums
  detect corruption or changes; they do not authenticate the publisher.
- Reject modified, incomplete, path-traversing, or unexpectedly versioned ZIPs.
- Retain the exact artifact and validation evidence used for each environment.

## Credentials and privileged access

Use encrypted SQLcl saved connections. Never pass a database password on the
command line or store wallets, connection definitions, private keys, tokens, or
secrets in this repository. Separate the target-schema connection from the ADMIN
connection and use ADMIN only for reviewed installation, cleanup or removal.

The standalone installer contains the target ADMIN actions. Review them before
execution; UPDATE does not perform ADMIN grant remediation. Apply least privilege in OCI
IAM and database grants; broad roles or unrestricted cross-schema access are not
part of the supported deployment.

## Authentication and authorization

Oracle APEX provides authentication. Configure Oracle APEX Accounts or an
approved external identity provider, enforce multifactor authentication where
required, and verify redirect/logout/session policy. Application authorization
must distinguish administrators from ordinary users. Review both roles after
installation and every identity-provider change.

Follow [Users, groups and workload access](access-control.md) for the role matrix,
APEX account setup, SSO mapping, individual workload assignments and revocation.
Test access using an ordinary user's session; an administrator seeing a page
does not demonstrate that restricted-user access is correct.

## OCI Resource Principal

OCI service calls use the database Resource Principal. Dynamic-group and IAM
policy scope determines what the application can reach. Limit policy by service,
compartment, and operation; review it periodically and after new features are
enabled. No application setting can compensate for an unnecessarily broad IAM
policy.

## Data and AI services

Cost, usage, resource, tag, prompt, response, and generated-SQL data may be
sensitive even when it is not a password. Classify it, restrict access, set
retention, and sanitize logs or support reports. Confirm which information an
external AI service receives before enabling assistant features, and disable the
feature if organizational policy does not allow that transfer.

## Network, backup, and monitoring

Restrict database and APEX network access, keep Oracle services patched, protect
backup locations, and monitor authentication, privileged configuration, job,
deployment, and assistant audit events. Test restore procedures and incident
response in an authorized disposable environment.

Report suspected vulnerabilities through [the security policy](../SECURITY.md),
not a public issue.

## Network and data-flow inventory

| Boundary | Operator responsibility |
| --- | --- |
| Browser → managed APEX | Protect authenticated requests, filters, reports and conversations with approved HTTPS/session policy. |
| Browser → `cdn.jsdelivr.net` | The application references pinned Tabulator, ECharts, jsPDF, DOMPurify, Marked, QR Code Generator and ExcelJS. Blocking the CDN can break UI functions. |
| Browser → Google Fonts | Material Symbols CSS is referenced at `fonts.googleapis.com`; font delivery may require Google's asset hosts. Check actual browser requests. |
| Database → OCI | Report reads, discovery, metrics and optional services use configured access. IAM and connectivity are separate checks. |
| Database → AI service | Prompts/context and responses may cross this boundary. Review enabled providers, data categories, retention and approved dataset scope before use. |
| Database → Email Delivery | Protect recipient/message data and sign-in-protected export links. Prevent clones from sending production mail. |
| Browser → downloaded files | Exports can contain identifiers, tags, costs and inventory. Application authorization cannot control a downloaded copy. |

PrismJS is embedded; not every dependency is. See [Third-party notices](../THIRD_PARTY_NOTICES.md).
A private database endpoint does not make browser CDN or AI calls private. Check
the intended browser network policy without disabling TLS/CSP protections.

## Retention and incidents

Assign retention owners for ingestion logs, assistant conversations, diagnostics,
deployment history, exports, mail and backups. No single setting controls all of
them. Current UI behavior includes seven-day completed-export availability and
ten-day terminal-email cleanup; verify other stores separately.

For suspected disclosure, contain the affected account/feature, preserve private
evidence and involve OCI/database/identity owners. Rotate exposed credentials
through their owning service and review sessions/permissions. Do not delete logs
to conceal a failed check. Use the private security-reporting route.
