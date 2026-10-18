# Target installer

Run this tool from the repository root after reading [Installation](../../docs/installation.md).
It is a self-contained Bash script with embedded bundle validation. No separate
validation library, development checkout or ADMIN helper is needed.

```bash
bash db/deployer/install_mc_bundle.sh --help
bash db/deployer/install_mc_bundle.sh --interactive
```

Supported modes:

- INITIAL creates the target schema, maps its workspace, installs the supplied
  bundle and establishes the first application administrator.
- UPDATE compares target receipts with the supplied release and applies the
  selected changes. It requires a target with the `mc-delta-2` engine.
- CLEANUP removes resources proven to belong to a failed INITIAL attempt.
- UNINSTALL permanently removes an installed environment after explicit confirmation.

Use [Upgrade](../../docs/upgrade.md) for CLI and browser update instructions.
Use [Recovery and removal](../../docs/recovery-and-removal.md) for the destructive
modes. Do not use either as an upgrade rollback.

INITIAL needs a new absolute private journal directory for a real run. It records
changes needed for failed-install recovery. Keep it in persistent storage outside
this checkout, not under `/tmp` or another automatically cleared directory. Its
parent must already exist; the installer creates the journal with mode `700`.
See [journal preparation](../../docs/installation.md#prepare-the-installation-journal).
Passwords are entered at prompts, never in command flags.
UPDATE uses the target saved connection only; ADMIN grant remediation is not
supported in this mode. UPDATE `--dry-run` writes planning metadata but does not
apply selected application objects.

The installer requires Bash 3.2+, SQLcl, an accessible Java 17+ runtime, `zip`,
`base64`, a SHA-256 utility and standard POSIX command-line utilities. Its
embedded archive reader checks file types and size limits before extraction,
and enforces output limits while reading. It does not download code. Git,
Python and a Java compiler are not installer prerequisites.
