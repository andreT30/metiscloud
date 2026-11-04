# Cost Intelligence

Cost Intelligence adds saved cost comparisons, future report-cost estimates and
statistical reviews of unusual costs to Analysis. Home links to the matching
saved forecast and unusual-cost views. These features need configured scopes,
eligible history and a successful background refresh; installing the bundle
alone does not produce results.

## Choose a reporting scope

Open **Analysis → Cost intelligence** and select one billing account and currency,
with an optional exact named workload. Your current workload permissions still
apply. Forecast is the default view; choose Changes or Anomalies for those tasks.

Advanced filters, individual resource selections, Cloud Advisor's resource-cost
context and combined workload scopes are not supported by these saved results.
An unavailable selection is not silently broadened to the whole account. Keep
using Overview, Daily Usage or Create Export for those reporting scopes.

Refresh reads published results. It does not load source reports, train models
or enable schedules. An unavailable amount is not zero cost. Read the saved
period, publication time, data cutoff and coverage notices before interpreting it.

## Compare historical costs

In **Changes**, **Latest saved comparison** selects the newest available saved
comparison for your exact scope, independently of Overview's dates. The recurring
producer compares the latest seven mature days with the preceding seven days.
Mature history ends four UTC days before the current day.

Read both periods in **Published comparison (UTC)**. Choose **Match page dates**
only for an exact saved match to the page's From/To dates. If none exists, use
**Use latest saved comparison**, or retain the unavailable result.

Compare the observed amount, comparison amount and signed difference. Daily
rates help when periods differ in length. The contribution table provides detail,
not a substitute total. **Details** shows quantities, effective rates and other
comparison evidence. Arithmetic differences do not prove their cause, and
contact tags do not establish verified ownership. Search and sorting apply only
to the current table page; use Previous/Next for other rows.

## Read a forecast

In **Forecast**, choose **Rest of month**, **3 months** or **6 months**. These are
saved estimates of future normalized report cost, not invoices or credit balances.
Rest of month begins tomorrow and ends on the current UTC month's final day.
On that final day, there are no remaining future days and the result is unavailable.

The daily chart distinguishes recorded costs from future estimates. Recent
unsettled days and missing observations remain gaps. Monthly view offers
**730-hour equivalent** for rate comparison or **Calendar total** for saved calendar
amounts. A 730-hour equivalent scales the known amount by its represented hours;
it is not a recalculated bill. Partial months remain partial even after scaling.
Spend cards, future-period totals, daily predictions and scenarios retain their
calendar amounts.

Expand **Model details and limitations** for the selected method, training dates
and diagnostics. Forecasts use up to 13 active calendar months of eligible
history. Retrospective error metrics are not confidence scores or guarantees of
future accuracy. Unknown provider coverage and missing history remain explicit;
the application does not invent uncertainty ranges or savings probabilities.

**Queue saved evidence export** requests the selected saved evidence. Treat the
download as operational data and store it privately.

## Investigate unusual costs

**Anomalies** shows saved findings for the authorized scope. The statistical review
assesses the latest seven mature UTC days against earlier eligible observations.
It is separate from qualified operational incidents. Statistical flags identify
patterns to investigate, not confirmed billing errors, outages or realized savings.

Read assessed-day coverage alongside the counts. Seven assessed days can support
a genuine zero count; insufficient or partial assessment shows N/A. Missing days
are never filled with zero. Home's unusual-cost summary links to the matching
saved review and dates. A revoked or changed scope can make an old link unavailable.

Statistical findings do not enable notification delivery. Any separately available
legacy incident evidence retains its own coverage and calibration requirements.

## Compare a consumption scenario

Expand **Consumption scenario**, select a saved forecast and future start/end
dates within its window, then enter a percentage from -100 to 1000. Select
**Calculate scenario** to record the scenario with your identity and assumptions.
This is an explicit write; simply reading or refreshing a forecast creates none.

The scenario is a deterministic percentage overlay. It does not change OCI
resources, alter the baseline forecast or establish a recommendation or saving.
Budget and credit-risk measures remain unavailable where their required period,
allocation or coverage evidence cannot be established.

## Prepare and operate Cost Intelligence

This section is for the DBA and application administrator. Native machine learning
runs inside the database; no Python runtime, notebook or external ML service is
required. The application owner needs `CREATE MINING MODEL` and
`EXECUTE ON SYS.DBMS_DATA_MINING`, together with the release's other prerequisites.
Do not grant mining privileges to individual APEX users or use broader ANY MINING
privileges. The DBA must review algorithm availability, quota, temporary space
and resource capacity on the actual target.

Run the installer's read-only report from a fresh direct application-owner
connection, outside APEX and without a proxy:

```bash
bash db/deployer/install_mc_bundle.sh \
  --mode OML_READINESS --connection MC_TARGET
```

It needs no bundle or ADMIN connection and does not grant privileges, capture
data, train models, publish results or enable jobs. Successful exit means the
report ran; inspect each section. Runtime and scheduled execution can remain
UNVERIFIED. NOT_CONFIGURED, BLOCKED and UNAVAILABLE require investigation.

Configure the intended account/currency scopes and methods explicitly through
the schema-owner configuration APIs:

| API | Configuration responsibility |
| --- | --- |
| `MC_COST_INTEL_DATA_PKG.CREATE_SCOPE` | Exact billing account, currency and optional named-workload membership; retain the returned scope ID. |
| `MC_COST_INTEL_OML_PKG.CONFIGURE_POLICY` | One reviewed method policy for each of `MONTH_END`, `MONTH_3`, `MONTH_6` and `DAY_30`. Supply retrospective selection evidence with provenance `RETROSPECTIVE_OPERATOR_EVIDENCE`, a `selectionReason` and an `evidenceReference`. Commit configuration deliberately. |

Supported method codes are `DAILY_GLM`, `DAILY_XGB_AR`, `ESM_SIMPLE` and
`ESM_WEEKLY_ADD`. ESM is restricted to `MONTH_END` and requires a `DAILY_GLM`
bridge fallback. Select methods from reviewed evidence for the intended data,
not simply to clear an unavailable message. Account identifiers, memberships,
policies and evidence belong to the target and are not supplied as demo seeds.

Forecast training needs at least 64 valid observations; lagged methods need
complete feature rows, and statistical review needs eligible earlier training
history separate from its assessment days. A row count alone does not prove
readiness. Check normalized history and reporting coverage first.

After separate authorization, a DBA can run
`MC_COST_INTEL_WORKER_PKG.RUN_OML_SCOPE` for one selected scope from a fresh direct
owner session with no existing transaction. It commits its capture, build and
publication phases; a later rollback cannot undo them. Verify saved results for
that scope and both allowed and denied user access before recurring activation.

The **Cost Intelligence** job starts disabled. Its supplied action processes at
most two configured scopes per run, not necessarily the two you intend to check.
Review its scope selection, daily schedule and first actual Scheduler execution
before relying on it. Installation, UPDATE and the read-only report do not
activate this job or notification delivery.

Use [Administration](administration.md#schedules-and-running-jobs) for schedule
controls and [Troubleshooting](troubleshooting.md#cost-intelligence-is-unavailable)
for missing results. Retain diagnostic and model-cleanup evidence privately;
never remove unrelated database models to make a refresh pass.
