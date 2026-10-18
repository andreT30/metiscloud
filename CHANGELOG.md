# Changelog

Public distribution changes start with this baseline. See [VERSION](VERSION)
for the bundled application version and the [upgrade guide](docs/upgrade.md)
before updating an installation.

## Unreleased

### Documentation

- Consolidated infrastructure, requirements and INITIAL instructions into
  Install MetisCloud, and combined first sign-in, Setup and settings into
  Set up and configure MetisCloud. Verification steps are included where needed.
- Recommended Autonomous AI Lakehouse and added generic IAM and SQLcl examples,
  persistent recovery-journal guidance and restricted-user verification.
- Removed redundant guides and updated navigation. The application bundle is unchanged.
- Explained INITIAL command options, defaults and prompts, including the
  distinction between guided input and parameterized installation.

## 26.10.18.02 - Initial public baseline

### Included

- MetisCloud application bundle for Oracle Autonomous Database with managed APEX.
- A self-contained installer for INITIAL and UPDATE, with guarded failed-install
  cleanup and uninstall modes.
- Archive validation that rejects links and special files before extraction,
  enforces size limits, and checks the extracted data.
- Cost and usage reporting, resource inventory, workload budgets, subscription
  credits and collected availability metrics.
- Optional AI assistance and in-app page help.
- Installation, Setup, access-control, administration, backup, recovery and
  troubleshooting guides, plus a product overview video.
- PolyForm Noncommercial 1.0.0 license and third-party notices.

### Deployment notes

UPDATE requires a target deployment manager that supports `mc-delta-2`.
An update can replace customized prompts, guardrails, semantic definitions and
page help. Review the [replacement and retention rules](docs/upgrade.md#what-update-replaces-and-preserves)
and take a recovery checkpoint before applying it.

### Known limitations

The legacy Table Helper shortcut has no standalone destination. Use
**Dataset Configuration → Source** for table metadata and
**Advanced Diagnostics → Native Schema Probe** for schema investigation.

Follow [Installation](docs/installation.md) and
[Set up and configure MetisCloud](docs/configuration.md) for your environment.
