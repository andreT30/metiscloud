# Upgrade an existing installation

Use the supplied, verified `bundle-app.zip` and the installer supplied with this
release. UPDATE first prepares the deployment tools, then applies the separately
planned application changes. It requires the supported permanent update entry
and the `mc-delta-2` application foundation. An older installation may need a
matching compatibility release and its **Enable UI updates** step first. If the
required entry is missing or rejected, stop and request the supported transition
for that installed baseline. Do not run INITIAL over it or copy database packages
manually. The full release ZIP alone is not a compatibility transition.

## Before changing the target

1. Read the [changelog](../CHANGELOG.md).
2. Have the DBA prepare a recoverable [schema backup](backup-and-recovery.md).
3. Verify the bundle against its supplied checksum, obtained from a trusted source.
4. Schedule a maintenance window. Record application version, target app ID,
   workspace, offset, authentication and scheduler states privately.
5. Stop conflicting work deliberately. Disabling a schedule does not stop an
   already running load. Do not overlap UPDATE with ingestion or another deployment.
6. Review local bot prompts, guardrails and semantic definitions against the
   ownership rules below. Keep a private record of changes that need reassessment.

## Preview with the installer

Run from the repository root:

```bash
shasum -a 256 -c bundle-app.zip.sha256
bash db/deployer/install_mc_bundle.sh \
  --mode UPDATE --bundle bundle-app.zip \
  --connection MC_TARGET --dry-run
```

Replace `MC_TARGET` with the target schema's SQLcl saved connection.
UPDATE preview connects to the database and retains planning metadata for the
deployment-tool comparison. It starts no preparation or application worker and
does not apply their changes. It does not produce the later application plan
before preparation completes. **It is not read-only.**

Review planned changes and ownership conflicts. Ordinary UPDATE uses only the
target connection and reports exact missing grants for separate DBA action.
This release requires `CREATE MINING MODEL` and execution access to
`SYS.DBMS_DATA_MINING` for the application owner. Do not substitute broad roles
or ANY MINING privileges.

## Apply the plan

```bash
bash db/deployer/install_mc_bundle.sh \
  --mode UPDATE --bundle bundle-app.zip --connection MC_TARGET
```

This real UPDATE authorizes both deployment-tool preparation and application
execution. It follows preparation progress before submitting the application
plan. For separate review and confirmation between those stages, use the
[application workflow](#apply-through-the-application).

If a DBA explicitly approves automatic remediation of the reported missing
grants, the CLI also accepts the following paired options:

```bash
bash db/deployer/install_mc_bundle.sh \
  --mode UPDATE --bundle bundle-app.zip --connection MC_TARGET \
  --apply-missing-grants --admin-connection TARGET_ADMIN
```

Use only a reviewed ADMIN saved connection for the same target. These options
authorize the installer's bounded grant policy, not arbitrary administrative SQL.
Ordinary application administrators can instead have the DBA apply the exact
reported statements and use **Retry prerequisites** in the application.

The target compares installed receipts and object revisions with the release.
Missing objects are created, newer revisions update owned objects, unchanged
revisions are skipped, and explicit approved removal entries can remove
receipt-owned objects. Identity, lineage, revision conflicts and collisions with
unowned objects stop the operation.

APEX is imported when its release version changes. Shared application definitions
have a separate seed step; they can change even when no database object revision
changes. Do not treat local edits to release-managed definitions as protected
customizations.

## What UPDATE replaces and preserves

| Content | UPDATE behavior |
| --- | --- |
| Bot prompts, instructions, tuning, schemas, feature flags and guardrails | Replaced by release definitions across all dataset/environment scopes, including LATEST and inactive entries. Review customized behavior before and after updating. |
| Provider resources | Existing target project, model, compartment, endpoint and vector-store settings, including their activation state, are retained. Missing settings receive empty placeholders. |
| Resource references inside configuration JSON | Target model, project, compartment, endpoint, region, credential, file and vector-store references are retained while portable settings are refreshed. |
| Semantic definitions | Release datasets, fields, joins, synonyms, periods and test prompts are loaded, including inactive and unapproved definitions with their supplied review status. Target schema references are remapped. Previously release-installed definitions absent from the new snapshot are deactivated. |
| Column Indexer | Rules are refreshed across dataset scopes. Existing target-derived value lists remain; new rules start empty. UPDATE does not reprofile values. The target's nightly refresh rebuilds them. |
| Page help | Help for this application is replaced, removed entries are deleted and the search index is rebuilt. Other applications' help and local usage/audit records remain. |
| Scheduler descriptions | Descriptions of existing registered jobs are refreshed, including cleared descriptions. This metadata step leaves schedules, arguments, history and enabled state unchanged and skips missing jobs. |

The protected provider parameters include `OPENAI_PROJECT_ID`, `GENAI_PROJECT_ID`,
`PROJECT_ID`, `LLM_MODEL_ID`, `LLM_COMPARTMENT_ID`, `LLM_ENDPOINT` and
`GENERIC_AGENT_VECTOR_STORE_IDS_JSON`. Empty placeholders still need configuration
before their feature can work. Semantic editor draft sessions, test-run history
and audit records remain local; retained drafts do not protect the installed
definitions from replacement.

## Apply through the application

An administrator can also use **Import / Update App**:

1. Open **Import & Deploy**, select the verified ZIP and choose **Upload and inspect**.
2. Review the deployment-tool preview and choose **Prepare manager** once.
   This changes the update tooling, but does not apply application changes.
3. Resolve any reported prerequisites, then review the application plan. After
   DBA grants, use **Retry prerequisites** without uploading the ZIP again.
4. Choose **Queue selected UPDATE** once and confirm the application changes.
5. Follow **Runs** for the object count, seed step, APEX step and final result.
   Open **View log** to inspect progress. The queued operation continues beyond
   the browser request; a slow response is not a reason to queue another UPDATE.

The UI cannot install a new empty environment. Ordinary UPDATE does not take a
backup or repeat full-archive validation, so validate the supplied artifact
before uploading it. Bundle Storage retains compact delta archives and historical
bundles; neither is a backup of target data.

After an interruption, reopen the page and inspect the retained operation. Use
**Resume preparation** when offered, rather than submitting a duplicate release.
The CLI can resume its captured phase when rerun with the same bundle and target.
Retained failure history is not permission to discard an unfinished operation.

## Check after UPDATE

Verify target APEX identity and authentication, then sign in with an administrator
and an ordinary user. Check workload restrictions, a known report, compilation
results, load monitoring and intended job states. Existing scheduler states are
retained and new application jobs remain disabled; review them before resuming
maintenance-paused schedules. Test optional AI/email separately when enabled.

For **26.11.02.01**, run the read-only Cost Intelligence readiness report and
review the new disabled job, target scope/method configuration and eligible
history. Training and scheduled activation are separate authorized operations.
Verify saved results with an Administrator and a restricted User. Follow
[Cost Intelligence](cost-intelligence.md#prepare-and-operate-cost-intelligence).

Review Bot parameters and semantic readiness after the seed step. Test a known
question, an out-of-scope request and a restricted user's access. Check that
target provider references remain correct and that changed prompts or guardrails
meet your policy. Review and reapply older saved Setup configuration before
relying on its previous readiness result. Verify that page help opens and searches
work for the installed application ID.

Oracle DDL can commit before a later step fails. Preserve the run ID and exact
failed object, inspect receipts/current state, and have the DBA approve any retry.
A blocked historical run does not resume automatically through the new route.
Installing an older ZIP is not rollback. Use [Backup and recovery](backup-and-recovery.md)
when safe reconciliation cannot be established.
