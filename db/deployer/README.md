# Target installer

Run this tool from the repository root after reading [Installation](../../docs/installation.md).
The [INITIAL options reference](../../docs/installation.md#initial-command-options)
explains each setting, default and prompt.
It is a self-contained Bash script with embedded bundle validation. No separate
validation library, development checkout or ADMIN helper is needed.

```bash
bash db/deployer/install_mc_bundle.sh --help
bash db/deployer/install_mc_bundle.sh --mode INITIAL --interactive
```

Supported modes:

- INITIAL creates the target schema, maps its workspace, installs the supplied
  bundle and establishes the first application administrator.
- UPDATE compares target receipts with the supplied release and applies the
  selected changes after preparing its deployment tools. It requires the supported
  permanent update entry and `mc-delta-2` application foundation. Older targets
  may need a matching compatibility transition first.
- OML_READINESS reports Cost Intelligence prerequisites and saved readiness;
  it does not train models, change grants or enable jobs.
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
Ordinary UPDATE uses the target saved connection only. Explicit DBA-authorized
grant remediation requires both `--apply-missing-grants` and
`--admin-connection NAME`; otherwise apply the reported grants separately.
UPDATE `--dry-run` writes deployment-tool planning metadata but starts no
preparation or application worker. See [Upgrade](../../docs/upgrade.md).

The read-only report needs neither a bundle nor an ADMIN connection:

```bash
bash db/deployer/install_mc_bundle.sh --mode OML_READINESS --connection MC_TARGET
```

Run it in a fresh direct application-owner connection. A successful report is
not proof of successful model execution. See [Cost Intelligence](../../docs/cost-intelligence.md).

The installer requires Bash 3.2+, SQLcl, an accessible Java 17+ runtime, `zip`,
`base64`, a SHA-256 utility and standard POSIX command-line utilities. Its
embedded archive reader checks file types and size limits before extraction,
and enforces output limits while reading. It does not download code. Git,
Python and a Java compiler are not installer prerequisites.
