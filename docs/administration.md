# Administration

Administrators maintain access, workloads, configuration, schedules and data
freshness. OCI and database administrators own platform access, IAM and recovery.
Complete [Setup and initial verification](configuration.md) before enabling recurring work.

Open Administration in the navigation menu, then App Config for the settings,
user access, workload, scheduler, email and load-monitoring tabs described below.
Operations overview and the bot tools are separate navigation entries.

## Daily checks

1. Open Data Load Runs. Apply a run ID, job or status filter, or leave them blank
   for recent results. Record the first failed step.
2. Open Logs for that run. Run Logs is read-only evidence; keep the run ID with
   the incident.
3. Inspect Daily Loads for missing or unexpectedly small daily row counts.
   Compare with available source reports before interpreting lower costs.
4. Review scheduler history and email failures.
5. Spot-check a known workload and resource.

Opening a tab does not start a job. Refresh updates the active tab. Table search
narrows loaded rows; server filters determine which rows were fetched. JSON
downloads can contain operational information and belong in protected storage.

## Schedules and running jobs

In Scheduler Jobs, inspect the exact schema-owned job and choose Manage.
Review its action, repeat interval and recent history before enabling it.
Record the schedule's owner and failure response. A blank repeat interval makes
the job non-repeating. Disabling a job does not stop an active execution.

Run starts a job immediately after confirmation. Do not overlap initial history,
nightly ingestion or deployment. New application jobs start disabled; the internal
installation recovery monitor is an exception. Retention/archive/expiry jobs need
individual approval because they can remove data.

The **OCI Limits** job collects the service-limit snapshot nightly. Review its
OCI permissions and enable the new job after installation or UPDATE when ready.
**OCI Tools → Limits & Usage → Refresh** only reloads saved data. If collection
fails, the previous snapshot is retained and marked stale; inspect the job's
run history before retrying. See [Limits and usage](usage.md#limits-and-usage).

The **Cloud Advisor refresh** job collects saved recommendations and evidence.
It starts disabled and is supplied with a daily 05:00 schedule under the
Scheduler's timezone. Before enabling it, confirm active OCI Cloud Advisor
enrollment, the configured tenancy/home region, approved Optimizer read access
and current inventory. The supplied collector supports commercial OCI regions;
other realms require verified endpoint support. Page Refresh does not run the
collector. A failed collection retains the previous complete snapshot; inspect
job history before retrying. Collection reads OCI data and never applies
recommendations. See [Cloud Advisor](usage.md#cloud-advisor).

Editing a PL/SQL block or stored-procedure action uses the job owner's privileges.
Require DBA review. The UI does not create or drop jobs.

In Data Load Runs, **Stop job** applies only to verified active Scheduler
execution and requires a reason. It is graceful and does not undo committed
data. Refresh to confirm the outcome. **Mark failed** is for an execution that
has ended while its record remains RUNNING; it is not a process kill.
Investigate **Execution not verified** before any database correction. These
controls do not stop manual SQL sessions. Access changes and intervention reasons
are audited.

## Workloads and budgets

Choose **Workloads → Add Workload** and a unique name. Select one scope:

- Manual: select explicit compartments.
- Application Tag: select one configured Cost Center tag value.
- Top compartment: select a hierarchy root and preview its descendants.

Top membership refreshes with nightly compartment inventory. A failed refresh
keeps the last valid membership and shows an error; do not interpret it as a
successful scope change.

A monthly budget is optional: enter a positive amount with at most two decimals
and a three-letter currency. Leaving it blank removes both budget fields.
Validate cost and membership in Analysis. Names cannot be renamed in place.
Before deleting a workload, revoke individual assignments and check reports that
depend on it. Deletion is permanent.

Use [User access](configuration.md#give-other-users-access) to assign workloads to
people. Do not confuse report definitions with authorization.

## Email queue

Complete [Email Delivery](configuration.md#email-delivery-and-exports-optional)
before enabling notifications. Queue Details shows safe status, attempts, error
category and request IDs without recipient addresses or message bodies.

A held delivery has an uncertain outcome and cannot be retried automatically.
Investigate with the email operator before taking another action to avoid
duplicates. Retry/Cancel applies only to supported conclusive states.
Delete Selected permanently removes terminal rows and their logs; active rows
must finish or be cancelled first. Terminal records are removed ten days after
completion, so retain incident evidence privately when needed longer.

Scheduled-job failure monitoring evaluates the latest recorded run after
monitoring was enabled. It does not backfill old failures, and a subsequent
success prevents an older failure being treated as current. Maintain another
monitoring channel in case email itself fails.

## Operations and feedback

The operations overview includes email reporting, activity, errors, page
performance, automation history, feature configuration and feedback. Feature
changes affect build options; validate the intended workflow before saving.
Hiding a feature does not fix its dependencies. Feedback deletion is permanent.

Subscriptions and Maintenance provide inventory; Active Orders permits selected
name/quantity corrections. These local changes do not change the OCI subscription.
Verify source completeness, billing period and currency before sharing forecasts.

## Assistant operations

Use [Assistant administration](assistant-administration.md) for model, semantic
dataset, index and file-search changes. Bot Logs provides run diagnostics.
Bot Operations separates Insights, Cost Metrics and Widget Insights, each with
its own filters. Its downloads cover the relevant loaded rows.

Widget support references such as `WID-123` can be looked up in Widget Insights.
Widget conversation text stays in browser memory and disappears on refresh or
navigation. Operational metadata is retained for six months; this policy does
not describe the separate main chatbot conversation stores.

Keep prompts, generated SQL and results private. Record sanitized run/request IDs,
release version, UTC time and scope when escalating an incident.
