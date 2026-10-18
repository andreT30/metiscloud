# Install MetisCloud

Use Oracle Autonomous Database with managed APEX. Public and private endpoints
use the same procedure; their connectivity checks differ. Complete
[environment preparation](prerequisites.md) before starting.

You need an unused target schema name, an ADMIN SQLcl saved connection, a workspace to
create or reuse, and a non-conflicting APEX application ID. Have an OCI
administrator available for IAM and a DBA available for database-wide decisions.
Do not install over an existing application schema.

## Get the distribution

Open the [MetisCloud repository](https://github.com/andreT30/metiscloud), select
**Code → Download ZIP**, and extract the download into a working directory.
Alternatively, clone the repository if you already use Git. Git is not required
to run the installer.

Open a terminal in the extracted directory containing `README.md`. Check that
`bundle-app.zip`, `bundle-app.zip.sha256`, `VERSION` and
`db/deployer/install_mc_bundle.sh` are present. Keep the application bundle ZIP
intact; the installer reads it directly. Do not mix a bundle with a checksum or
installer from a different distribution.

Keep wallets, journals and connection records outside this directory. Retain
the supplied distribution and version for your installation record.

## Verify the supplied release

Run from the repository root:

```bash
shasum -a 256 -c bundle-app.zip.sha256
```

Use `sha256sum -c bundle-app.zip.sha256` where `shasum` is unavailable.
Stop if the checksum check fails. It checks the ZIP against the supplied checksum
without connecting to your database. Obtain both files from a trusted source;
a checksum alone does not authenticate the publisher. The installer performs
additional bundle validation before applying the release.

The expected checksum result is `bundle-app.zip: OK`. Resolve a missing file or
checksum mismatch before connecting the installer to a database.

## Choose database and workspace settings

The installer distinguishes a new dedicated database from an existing shared
database. Choose `NEW` only when you authorize database-wide prerequisite
configuration, including Resource Principal and automatic indexing settings.
Choose `EXISTING` for a shared database; missing database-wide prerequisites
require separate approval. Neither option creates the Autonomous Database.

Choose `CREATE` for a new APEX workspace or `EXISTING` for a workspace approved
to map the new schema. A workspace administrator and the first MetisCloud
administrator are separate accounts/roles. The installer detects workspace
builder authentication; do not enter an external identity-provider password.

## Prepare the installation journal

INITIAL writes a recovery journal on the computer running the installer. It
records the target identity, completed changes and recovery information so
CLEANUP can identify resources created by a failed installation. This is an
operational record, not a database backup.

Choose a persistent private directory outside the distribution, for example
`/path/to/private/metiscloud/initial-001`. Replace the whole example path with
a location owned by the installer operator. Create its parent directory first
and restrict access to that operator. Leave `initial-001` absent: the installer
creates it with owner-only permissions (`700`). Use a new name for each attempt
and retain the original journal if an attempt fails.

Do not use `/tmp`, `/var/tmp`, a disposable container filesystem or another
location subject to automatic cleanup. Recovery may be needed after a reboot
or in a later session. The path must be absolute, use letters, digits, `_`, `-`,
`.` and `/`, and contain no spaces, traversal components or symbolic links.
Keep the journal after installation according to your operational retention
policy and protect any retained copy. Use the original location for
[failed-install recovery](recovery-and-removal.md#clean-up-a-failed-initial).

## Run the guided installer

```bash
bash db/deployer/install_mc_bundle.sh --interactive
```

Choose INITIAL. Have these values ready:

- The path to `bundle-app.zip` and the ADMIN saved connection name.
- The new schema and its SQLcl saved-connection name.
- Database mode, workspace name/mode and workspace administrator where needed.
- Target application ID and APEX offset policy. Use `AUTO` for a new installation.
- The first MetisCloud administrator's username, password and email.
- A new absolute journal directory in private, persistent storage outside this repository.

Review the summary before confirming the exact schema name. Password prompts
occur during preparation, before target mutation. If replacing an existing saved
connection, review the prompt; its backup is retained until success or cleanup.
Protect the journal and terminal output as operational records.

For repeatable parameter selection, this example still prompts for secrets and
any omitted account details:

```bash
bash db/deployer/install_mc_bundle.sh \
  --mode INITIAL --bundle bundle-app.zip \
  --database-mode NEW \
  --admin-connection TARGET_ADMIN \
  --target-connection-name MC_TARGET \
  --schema MC_TARGET --confirm-create-schema MC_TARGET \
  --workspace MC_WORKSPACE --workspace-mode CREATE \
  --first-admin MC_ADMIN \
  --target-app-id 2300 --apex-offset AUTO \
  --journal-directory /path/to/private/metiscloud/initial-001
```

Replace every example name and the journal path with your approved values.
Use the [example-name reference](infrastructure.md#names-used-in-the-installation-examples)
to distinguish the database, workspace and application accounts. The journal
directory must be new and its parent directory must already exist. Choose a
private writable location and let the installer create the journal directory.
The example leaves the workspace administrator unspecified so the installer
can prompt after detecting Builder authentication. For Autonomous Database
sign-in, choose the new target schema account or an existing eligible database
account as instructed by the prompt. Do not invent a separate database account
name that does not exist.

For a shared database, use `EXISTING` and arrange prerequisite approval
with its DBA rather than copying the `NEW` choice above.
INITIAL `--dry-run` previews the phases without creating the schema or importing
APEX; it cannot prove OCI access or database compilation.

## Check the result

Read the final summary and review compilation warnings before admitting users.
Confirm the target application ID, workspace and first administrator. Application
jobs start disabled; the install recovery monitor is an internal exception.

Ask the workspace administrator to open App Builder in the target workspace and
confirm that the installed application is present with the chosen ID. Run it to
obtain the application sign-in URL. Keep that URL in your private deployment
record and use it for [first sign-in](first-use.md#sign-in-as-the-first-administrator).

Keep the journal after success under your operational retention policy. If
installation fails, preserve it and follow [failed-install cleanup](recovery-and-removal.md).
Do not repeatedly run INITIAL against a partially created schema.

Continue with [First sign-in and setup](first-use.md). Installation prepares the
application; Setup configures OCI access and loads your data.
