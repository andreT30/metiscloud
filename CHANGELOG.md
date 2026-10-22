# Changelog

Public distribution changes start with this baseline. See [VERSION](VERSION)
for the bundled application version and the [upgrade guide](docs/upgrade.md)
before updating an installation.

## 26.10.22.03 - Cloud Advisor

### Added

- **OCI Tools → Cloud Advisor**, an information-only view of saved OCI
  recommendations, grouped by type with affected resources and recorded evidence.
- Workload and search filters, source estimates in their recorded currency,
  recommendation/resource CSV downloads, and links to authorized inventory
  and matched resource cost history.
- Daily **Cloud Advisor refresh** collection. A failed collection retains the
  previous complete snapshot. The new job starts disabled.

### Changed

- Analysis retains the matched billing context when opened from Cloud Advisor,
  including compartment and creation-date restrictions for supported bucket
  matches. Ambiguous matches do not open an unfiltered report.
- OCI Tools loads Cloud Advisor without waiting for the other tools' shared
  startup requests. Resource details open from saved rows, with raw evidence
  loaded on request.

After installation or UPDATE, review Cloud Advisor enrollment, OCI read access
and inventory freshness before enabling **Cloud Advisor refresh**. MetisCloud
does not apply recommendations, record local decisions or claim realized
savings. See [Cloud Advisor](docs/usage.md#cloud-advisor).

## 26.10.18.06 - OCI Limits

### Added

- **OCI Tools → Limits & Usage** for administrators to review service limits,
  used and available quantities, utilization and collection status.
- Search and filters for service, scope, region, availability domain and usage,
  with details for each selected limit.
- Nightly limits collection with snapshot freshness and failure reporting.
  A failed rebuild preserves the previous snapshot and marks it stale.

After installation or UPDATE, review OCI access and enable the new **OCI Limits**
job when ready. Refreshing the page reads the saved snapshot; it does not start
collection or change OCI resources. See the [user guide](docs/usage.md#limits-and-usage).

### Documentation

- Consolidated infrastructure, requirements and INITIAL instructions into
  Install MetisCloud, and combined first sign-in, Setup and settings into
  Set up and configure MetisCloud. Verification steps are included where needed.
- Recommended Autonomous AI Lakehouse and added generic IAM and SQLcl examples,
  persistent recovery-journal guidance and restricted-user verification.
- Removed redundant guides and updated navigation.
- Explained INITIAL command options, defaults and prompts, including the
  distinction between guided input and parameterized installation.
- Clarified DBA ownership of schema backup and recovery, APEX reinstallation
  requirements and checks before returning to service.
- Added selected demo screenshots, linked the raw video for browser playback with
  a direct MP4 download, and documented issue reporting and contributions on both hosts.

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
