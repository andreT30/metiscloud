# Failed-install cleanup and removal

These operations can delete data. A DBA must verify the database, schema,
workspace and purpose before running them. For a failed UPDATE, use
[Backup and recovery](backup-and-recovery.md), not CLEANUP.

## Clean up a failed INITIAL

Keep the failed attempt's private journal. Use the same ADMIN saved connection
and the original journal directory:

```bash
bash db/deployer/install_mc_bundle.sh \
  --mode CLEANUP --admin-connection TARGET_ADMIN \
  --journal-directory /path/to/private/metiscloud/initial-001
```

CLEANUP uses ownership receipts to identify resources created by that attempt;
it does not need the original ZIP. Review its result and any remaining resources
before retrying installation. Do not fabricate a journal or point it at another
attempt. Preserve logs when cleanup cannot verify ownership.

## Permanently uninstall

Obtain approval for data loss and retain a tested backup if recovery is required.
Notify users, stop application activity and confirm that the workspace is
dedicated to this installation. The installer checks identity and sessions.

First preview the exact target:

```bash
bash db/deployer/install_mc_bundle.sh \
  --mode UNINSTALL --admin-connection TARGET_ADMIN \
  --schema MC_TARGET --workspace MC_WORKSPACE \
  --target-app-id 2300 --dry-run
```

Only after reviewing that preview, remove `--dry-run` and add
`--confirm-drop-schema MC_TARGET --acknowledge-data-loss`. This permanently
deletes the target schema/data and its dedicated workspace. Do not combine
preview with deletion confirmation.

No original installation journal or host name is required. An optional private
journal retains progress. `--target-connection-name` selects one saved connection;
otherwise the installer discovers and removes verified target-schema connections.
Check the final summary for anything it could not remove. Recovery depends on
your backups; the tool has no undo.
