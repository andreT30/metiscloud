# Deployment checklist

Complete these checks in your own environment before admitting users. Keep the
bundle version and hash, database/APEX/SQLcl versions, network mode, date and
results in a private deployment record. Do not publish endpoints or raw logs.

- Verify the bundle checksum and review the installer result, compilation status
  and scheduler state. Investigate failures before continuing.
- Complete Setup and check that the first load and any older-history collection
  finish. Confirm that scheduled loads resume as intended.
- Compare a known report with its source totals, currency, scope and units.
  Open a downloaded report and check its contents.
- Test Administrator, Contributor, assigned and unassigned User access. Confirm
  that disabled and unmapped identities cannot access the application.
- If using OCI SSO, repeat the access checks with real SSO sessions. Use new
  sessions after role changes.
- Check the installer and browser network routes, required outbound services and
  rejection of unauthorized access for your public or private endpoint.
- If enabling email, verify approved recipients and that report links require
  the intended sign-in and access checks.
- If enabling AI, test model connectivity and inference separately. Check that
  responses use approved datasets and respect user scope and data-sharing rules.
- Before UPDATE, preview the plan, resolve conflicts and take a recovery
  checkpoint. Afterwards, check configuration retention and replaced definitions
  using the [upgrade guide](upgrade.md).
- Rehearse restoration in an isolated environment with controlled schedules.
  Test cleanup or uninstall only in a disposable environment if you need them.

An installation completing successfully does not establish that every optional
feature or access policy is configured correctly. Use [troubleshooting](troubleshooting.md)
for failures and [security responsibilities](security.md) for ongoing controls.
