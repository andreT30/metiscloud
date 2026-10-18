# Install MetisCloud

Use Oracle Autonomous Database with managed APEX. Public and private endpoints
use the same procedure; their connectivity checks differ. Complete
[environment preparation](prerequisites.md) before starting.

You need a new target schema, an ADMIN SQLcl saved connection, a workspace to
create or reuse, and a non-conflicting APEX application ID. Have an OCI
administrator available for IAM and a DBA available for database-wide decisions.
Do not install over an existing application schema.

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
  --workspace-admin WORKSPACE_ADMIN --first-admin MC_ADMIN \
  --target-app-id 2300 --apex-offset AUTO \
  --journal-directory /secure/metiscloud/initial-001
```

Replace every example name and the journal path with your approved values.
INITIAL `--dry-run` previews the phases without creating the schema or importing
APEX; it cannot prove OCI access or database compilation.

## Check the result

Read the final summary and review compilation warnings before admitting users.
Confirm the target application ID, workspace and first administrator. Application
jobs start disabled; the install recovery monitor is an internal exception.

Keep the journal after success under your operational retention policy. If
installation fails, preserve it and follow [failed-install cleanup](recovery-and-removal.md).
Do not repeatedly run INITIAL against a partially created schema.

Continue with [First sign-in and setup](first-use.md). Installation prepares the
application; Setup configures OCI access and loads your data.
