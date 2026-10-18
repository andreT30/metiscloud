# Assistant administration

Use Chatbot Administration for parameters, approved semantic datasets, value
rules, router tests and reference files. These tools affect shared assistant
behavior. Review one focused change at a time and test it before wider use.

Releases replace shared prompts, tuning, feature flags, guardrails and semantic
definitions across dataset/environment scopes. Target service references and
derived value lists have different retention rules. Read
[UPDATE ownership](upgrade.md#what-update-replaces-and-preserves) before relying
on a local customization surviving an upgrade.

## Parameters and models

Choose the dataset and component filters in Parameters. The workspace uses PROD;
LATEST provides shared current settings where a dataset override is unnecessary.
Include inactive rows when investigating a missing setting. Search only filters
the loaded table.

Open a row's full detail before editing. Content may be JSON, a prompt, number,
switch or identifier; preserve its expected type and never store credentials.
Add Parameter belongs to chatbot configuration; the general application-setting
inventory is fixed. Component type cannot be
changed during edit. Delete is permanent.

The model picker sets the configured default model. An empty list needs a
discovery/configuration check, not an invented model identifier. Test model
invocation separately from connectivity/discovery. Prompt and guardrail changes
need safety review, including out-of-scope and restricted-user questions.
Do not disable guardrails just to suppress an error.

## Dataset Configuration

Semantic Builder maps business language to reviewed tables, fields and joins.
It does not edit source-table data.

1. Select a dataset, or create one with a unique code, name, owner and description.
2. Select the schema/table and retrieve metadata.
3. In Semantic Draft, review business names, descriptions, synonyms and
   dimension/measure/time roles. Treat AI suggestions as proposals.
4. Configure Relationships and any genuine custom Business Calendar Periods.
   Ordinary date ranges do not need a custom period rule.
5. Save the draft. Browser edits alone are not stored.
6. Run representative Question Tester cases, review readiness/validation findings
   and compare the draft with the approved definition.
7. Approve only after review. Saving a draft does not approve it for runtime use.

Use **Dataset Configuration → Source** to retrieve table metadata. The Table
Helper shortcut in Parameters has no working destination in this candidate.
Use **Advanced Diagnostics → Native Schema Probe** when investigating schema
metadata. Do not treat generated descriptions as verified business definitions.

## Column Indexer

Select a dataset, table and column when adding a value rule. Inspect the complete
JSON with More. Generated values are read-only; Edit changes enriched keywords.
Enrich requests additional keywords, which need review before use.

Invalid JSON or failed enrichment remains in the dialog so you can correct it.
Saving updates routing evidence. Delete removes the selected rule after
confirmation. Target values depend on loaded data, so investigate ingestion
before rewriting rules to compensate for missing values.

UPDATE retains target-derived value lists and scheduled processing rebuilds
them. Managed definitions can still change with a release; preserve important
configuration through your [backup policy](backup-and-recovery.md).

## Rules Tester

Select Dataset and Environment, enter a representative question and choose Test.
Read Match Summary first, then Router JSON. The test interprets routing; it does
not save configuration or execute end-user SQL. A no-match is a valid result.
Check active state, dataset scope and saved rules before blaming the model.

## Vector Store

Confirm that the intended vector-store mapping is configured for LATEST/PROD.
Only upload files approved for the generic assistant's audience and AI service.

1. Choose a file or drag it onto the dropzone.
2. Select Upload and Attach.
3. Refresh while processing continues. Do not upload duplicate copies because
   indexing is still underway.
4. Test a representative file-search question after processing succeeds.

Remove detaches the file but retains the OCI file. Delete permanently removes
the underlying OCI file and its local record after backend confirmation.
Read the selected file and scope before confirming. A failed operation needs
configuration/permissions review, not repeated destructive requests.

## Bot Logs

Choose Native Debug or Generic Debug, filter Recent Runs and select Inspect.
Start with Outcome and Why, then follow Journey. A later operation marked
Not reached may simply follow an earlier failure.

Call and Response explains recorded facts. Exact Evidence, Copy and Download
can expose complete prompts, context, SQL values, tool arguments and results.
Keep these records private. Log text remains untrusted even for administrators.

Incomplete evidence is not a conclusive diagnosis. Refresh, inspect All events
and retain the request/run reference if history remains incomplete. The viewer
does not invent missing responses or unreturned reasoning.

## Bot Operations

Insights covers run quality, model performance, errors and feedback.
Cost Metrics covers token use, model/user costs and spikes.
Widget Insights covers page-help activity and sanitized failures. Each tab
has independent filters; use Refresh/Reset in the tab you are investigating.
Downloads contain the relevant loaded rows, not every historical record.

Look up a widget reference such as WID-123 in Widget Errors using the relevant
date range. Widget telemetry excludes question/answer text, page excerpts,
raw provider responses and browser session identifiers. Its six-month metadata
retention does not apply to the separate main chatbot logs/conversations.

After a configuration change, test expected results, refusals and access scope.
See [Security](security.md) for data-handling responsibilities.
