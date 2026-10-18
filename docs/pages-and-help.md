# Pages and in-app help

Use this directory to find the right part of MetisCloud. Detailed controls,
limits, confirmations and troubleshooting are available inside the application.
Visible pages and actions depend on your role, workload assignments and enabled
features; a missing menu entry does not grant access through a direct URL.

## Get help on the current page

Select the **?** button in the page toolbar to open its help. Some workspaces
also provide focused help for individual tools or tabs. Start there for the
current page's controls, filters and warnings.

When configured and available, the **page-help widget** lets you ask questions
about the page you are using. It is separate from the main MetisCloud Chat
workspace used for data questions. Its conversation stays in browser memory and
disappears on refresh or navigation. Keep any displayed support reference if
you need an administrator's help; do not put secrets into a question.

The widget requires optional AI configuration. If it is unavailable, use the
page's **?** help and these guides. An assistant answer does not grant permissions
or replace the confirmation shown before a destructive action.

## Reporting and daily use

| Page | What it is for | Who normally uses it |
| --- | --- | --- |
| Home | Subscription credit health, forecasts and recent workload spending. | Administrators and Contributors. |
| Analysis | Cost and usage investigation by period, workload, service, compartment and resource; includes reporting downloads. | Administrators, Contributors and Users within their allowed scope. |
| OCI Tools | Resource inventory, analytics/topology, policies, compartments and price comparison. | Administrators and Contributors; Users have permitted workload-scoped views. Ratecard and Load Policies are administrator-only. |
| My Reports | Saved reporting scopes and recurring cost comparisons. Saved items belong to their creator. | Users with the required workload access, Contributors and Administrators. |
| Availability Metrics | Collected metric health, trends and coverage for the selected scope. Missing metrics alone do not prove an outage. | Users with reviewed workload access, Contributors and Administrators. |
| MetisCloud Chat / Metis Bot | Analytics questions against approved datasets, or Generic assistance with optional approved reference files. | Administrators and Contributors with configured assistant access. |
| My Notifications | Personal notification inbox, completed-file notices and budget alerts. | Admitted application users; each person sees their own records. |

For practical reporting steps and interpretation, read the [user guide](usage.md).

## Administration and operations

These workspaces require application administration rights.

| Page | What it is for |
| --- | --- |
| App Config / Application Configuration | Setup, application settings, user access, workloads, email, schedules and load monitoring, along with subscription and reference information. |
| Operations overview | Application activity, errors, performance, feature controls, automation history and feedback. |
| Bot Parameters / Chatbot Administration | Shared assistant configuration: Parameters, Dataset Configuration, Column Indexer, Rules Tester and Vector Store. |
| Bot Logs | Inspect recorded Native and Generic assistant runs, outcomes and detailed diagnostic evidence. Logs can contain sensitive context and results. |
| Bot Insights / Cost / Bot Operations | Assistant quality and feedback, token/cost metrics and page-help widget operational information. |
| Import / Update App | Upload a supplied release, inspect its plan, queue UPDATE and follow runs or stored bundles. A new installation uses the standalone installer first. |
| Source registry | Inspect installed database-object revisions and removal records when reviewing an installation or update. |

Use [Administration](administration.md) for routine operating tasks,
[Assistant administration](assistant-administration.md) for bot tools and
[Upgrade](upgrade.md) before changing the installed release.

### Find a task within App Config

- **Setup** guides initial configuration and loading. **Load Status** tracks work
  that still needs attention; finishing Setup does not guarantee older history
  has completed.
- **User access** manages local access and individual workload assignments.
- **Workloads** defines reporting scope and optional budgets.
- **Application settings** permits edits to existing, unprotected settings.
- **Scheduler Jobs**, **Data Load Runs**, **Logs** and **Daily Loads** support
  scheduling and investigation of collected data.
- **Email Delivery**, recipient settings, templates and **Email Queue** support
  optional mail configuration and delivery monitoring.

### Find a task within Chatbot Administration

- **Parameters** manages prompts, model settings and scoped configuration.
- **Dataset Configuration** prepares and reviews the meaning of tables, fields
  and relationships used for analytics questions.
- **Column Indexer** maintains value rules and enriched keywords.
- **Rules Tester** checks how a question is routed without executing end-user SQL.
- **Vector Store** manages approved files for reference search.

The retained **Table Helper** shortcut has no standalone destination in the
reviewed candidate. Use **Dataset Configuration → Source** for table metadata
and **Advanced Diagnostics → Native Schema Probe** for schema investigation.

## When help does not resolve the problem

Record the page, operation, release version and a sanitized run or support
reference. Ask the application administrator to inspect the relevant logs.
Use [Troubleshooting](troubleshooting.md) for installation, access, ingestion,
assistant and email failures, or the [FAQ](faq.md) for common questions.
