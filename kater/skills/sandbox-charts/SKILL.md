---
name: sandbox-charts
description: "Sandbox: charts. Use it when the person asks for: A saved chart of a query, as a dashboard's tile or an app's chart draws it."
---

This skill is Kater's guide `vfs://guides/sandbox-charts.md`.

# Sandbox: charts

A part of [Sandbox](vfs://guides/sandbox.md).

## Charts

A chart belongs to a statement that reads one model, on the `query` route, or one workspace, on the `workspace` route. The Sandbox draws it with the chart shell, the same one Explore, the editor and dashboards use: its pills change the statement, and its type and settings change the chart. It's kept in the file's `charts:` frontmatter, one entry per charted statement, so it's there the next time the file opens:

```yaml
charts:
- statement: 0
  fingerprint: "410042cb42e1"
  chart: {version: 2, kind: bar, type: axis, title: Spend by platform, category: platform, values: [sum__spend], series: union__currency, presentation: {orientation: vertical}}
```

- **`statement` and `fingerprint`** are the index and fingerprint a run returns in its `statement`. The fingerprint lets the entry find its statement again after statements move. Quote it, since one that's all digits would read as a number. A new entry can leave `fingerprint` out: the first save gives it the fingerprint of the statement it names.
- **`kind`** is the chart as drawn, and **`type`** the type the reader chose: `axis` for bars, lines and areas together, or a kind. Left out, the chart is the best fit for the rows.
- **Column names** are the statement's outputs. A bare name matches as the warehouse folds it, so `platform` and `PLATFORM` both name the output `platform`; a quoted one, such as `"Total Spend"`, matches exactly. The Sandbox writes them in lower case.
- **One entry a statement.** A second entry for the same statement is refused.

| `kind` | On screen | Roles |
| --- | --- | --- |
| `bar`, `line`, `area`, `composed` | Axis | `category`, `values`, and `series` to slice it |
| `ranked` | Ranked bars | `category`, `values`, `rank`, and optionally `series` and `comparison` |
| `multiples` | Small multiples | `category`, `values`, `series` |
| `pie` | Part of whole | `category`, `value`, and optionally `percentage` |
| `breakdown` | Breakdown | `category`, `value`, and optionally `percentage` |
| `metrics`, `stats` | Metric row, Stat cards | `values`, and optionally a percent-change `comparison`. Over more than one row, the figures are the latest period's, and each draws every row under it as a sparkline, which `presentation.sparkline: false` hides |
| `table` | Table | `columns`; left out, every output |
| `insight` | Text | `template`: its words in Markdown, with **bold** and *italic*, and a `<kater-value field="…"></kater-value>` tag for each figure, by the output's name. The fields its tags name are its values, so it needs no `values` |
| `treemap`, `sunburst`, `sankey`, `radar`, `movers`, `funnel`, `peak`, `cohort`, `radial`, `progress`, `target`, `status`, `updates` | Their own | `encodings`: each of the kind's encodings by name, such as a treemap's `levels` and `value` or a flow's `source` and `target`, with its output or outputs |

**Each value's own settings** go in `value_settings`, one entry for each of the chart's values in order: a pie's `value`, else `values`, else the `values` a `radar` or a `peak` names under `encodings`. An entry holds only what its value sets, and `{}` sets nothing:

```yaml
  chart: {version: 2, kind: stats, values: [week_spend, week_roas], value_settings: [{prior: week_spend_prior}, {label: ROAS, prior: week_roas_prior}]}
```

| Key | What it sets | Left out |
| --- | --- | --- |
| `label` | The value's own label, which the legend, the tooltip and a card show in place of its field's | The field's label |
| `mark` | For `composed` only: `bar`, `line`, `area` or `scatter` | The first value draws as bars and the rest as lines |
| `point` | A `scatter` mark's point: `circle`, `diamond`, `square` or `triangle` | A circle |
| `axis` | For an axis chart: `left` or `right`. Give it to every value or to none | The chart places each value by its unit and its size: a percent beside a count, or a value more than 10 times another, reads against the right axis |
| `stack` | The stack the value draws in: values in one stack pile up, and another stack stands beside it | The chart's own `stacking` |
| `prior` | For `metrics` and `stats`: the output the value is read against, such as the week before's, which the statement returns | No comparison with a prior period |

`title` and `description` are the reader's own, and `presentation` holds the chart's whole style, in the chart spec's own keys, such as `orientation`, `stacking` or `legend`. Kater adds no key to it: a `bar` with no `orientation` stands upright, so a chart opened in the Sandbox and saved with no change saves as it was. `stacked: true` is short for `stacking: stacked` on a `bar`, `area` or `composed` chart. A key the kind does not take is refused, with the key's name.

**The tooltip.** With no setting, a chart's tooltip shows its own fields. `tooltip` adds at most 6 fields after them, each by its output's name: `bar`, `line`, `area`, `composed`, `pie`, `radar`, `peak`, `ranked`, `multiples`, `treemap`, `sunburst` and `sankey` take it. A treemap or a sunburst shows the added fields on a part that is one row, not on a part that holds many. A dimension gives its output and `distinct`, the output that counts its values in each mark, so a mark that holds more than one shows "3 values". `presentation.tooltip` changes the rest: `hide` lists the chart's own fields that the tooltip leaves out, `labels` renames a field in the tooltip only, an added field's too, and `text` puts words above the rows, in Markdown with a `<kater-value field="…"></kater-value>` tag for each figure, as an `insight`'s template does. `presentation.tooltip: false` shows no tooltip. A field that the statement does not return, or a seventh field, is refused:

```yaml
  chart: {version: 2, kind: bar, category: region, values: [sum__revenue], tooltip: [count_distinct__order_id], presentation: {tooltip: {labels: {count_distinct__order_id: Orders}, text: "**<kater-value field=\"count_distinct__order_id\"></kater-value>** orders"}}}
```

**A tooltip field never groups.** A field added to the tooltip is never in the GROUP BY, so it never splits a mark or changes the chart's grain. A measure is worked out at each mark's groups, as the chart's own values are. A dimension is read as one value for each mark, with its MIN, MAX and COUNT DISTINCT: the tooltip shows the value when the mark has one, and how many values it has when it has more. This holds for every chart kind, and a `sankey`'s `details` are tooltip fields. Kater never groups by a tooltip field, as Vega-Lite and Tableau's Detail shelf do, since that splits each mark.

**A chart follows its statement.** Every save re-keys `charts:`, whether the file is saved from the Sandbox, VS Code, `write_file`, `edit_file` or `source.write`:

- **The same fingerprint at the same index:** kept.
- **The same fingerprint at another index:** moved there, so inserting a statement above a charted one moves its chart down.
- **Neither:** the statement was edited, so the entry keeps following the lines it was on, and takes the statement's new fingerprint.
- **A deleted statement:** its entry is kept past the last statement, detached. It finds its statement again if a statement with its fingerprint comes back.
- **No fingerprint:** a new entry takes the fingerprint of the statement it names.

**A draft of a saved query keeps its chart here too.** Each change that the line above a chart in Sandbox mode names makes the person's draft. A chart type or the chart's settings, such as one value's mark or side, change no SQL and run nothing, so they go in the draft's `charts:`, and a draft that changed only those holds the query's own SQL. A setting changed together with a field goes there too, and a field added with nothing else keeps no chart. Opening the draft draws that chart again (`query.draft` answers it as `chart`), and taking the change back removes the entry. A filter switched off is the reader's choice, and the file keeps none of it. [Changing a chart someone else made](vfs://guides/sandbox-changing-a-chart.md) says what each Save does with the chart.

**Its rows.** Dates run in order; otherwise the statement's own ORDER BY is kept, and categories run in order without one. The statement's LIMIT is the chart's, so "Top 20" appears in the title only when the statement asked for it.

**Changing a statement from its chart.** Each pill change is the statement's picks written again, as the field picker writes a query from the same picks: `sandbox.statement.read` reads a statement as its picks, with the fields and menus its chart offers, and `sandbox.statement.write` returns the statement's new text for the picks, which the Sandbox puts where the statement is, as typing does, with the file's `parameters:` as its `parameters` say (see [Conditions](vfs://guides/sandbox-scratch-files.md#conditions)). Switching a condition off writes nothing. A statement with SQL no pick makes, such as `WHERE amount > 10`, keeps its fields; change them in its SQL, and `sandbox.statement.read` says why in `unread`. A statement that reads a workspace, or one of its queries as a draft does, reads as the picks of the relation it selects from: that query's outputs, with their menus. A next question, or a chart type set up, opens as a new scratch file, in a tab of its own.

**Drawing one without the UI.** `kater scratch chart.preview --request-file -`, with the scratch file's `namespace` (written as in step 2 of [Writing a statement](vfs://guides/sandbox-scratch-files.md#writing-a-statement)), `path`, `statement` and, optionally, the `chart` to draw, `inputs`, with `filters` for the optional filters to switch on and `off` for those to switch off, and `limit`, at most how many rows (10,000 by default). It returns every output the statement has, planned for that chart, whose footer's finding it words; without one, or when it no longer fits the statement's outputs, the rows are the statement's own, as a table. From a terminal: `kater scratch chart.preview --path FILE --statement N`, with `--chart '{"version": 2, "kind": "bar", "category": "platform", "values": ["sum__spend"]}'` to try one.

## Adding a statement to dashboards

A statement becomes a saved query in a workspace, where stakeholders can reuse it, through Add to, the same Add to as every chart's. `kater add-to FILE --statement N` lists where it can go, and the same line with `--to` adds it, through `kater_apply` once the person agrees where.

- **It is a draft.** A statement has no file of its own, so Add to writes it as a query in each place first, with the parameters its file declares for it. Kater reads what it reads from the file, and the chart the file's `charts:` keeps for the statement too. `--no-chart` adds the query alone, and `--label` is a title the person wrote for the query and its chart, which names them. Without it neither has a label, so both show Kater's words, and the query is named from the statement's title.
- **Where it can go.** `kater add-to FILE --statement N` lists every place on its Dashboards tab: the workspace it reads, when it reads one, then each workspace over the same shared model you can change, each with its `fit` (`fits`, `pending` while Kater checks it, `refused` with its reason, or `other_model` for a workspace over another model, which only `--search` shows), the model's Explore page (`explore`) and where a new workspace would go (the tab's `new`). Kater keeps the fits by the statement's text, so a second read of the same text answers at once.
- **Adding.** `kater add-to FILE --statement N --to workspace:<folder> --to new:dashboards --name "<name>"`, through `kater_apply`, adds to all of them in one commit: the query, and with a chart, the chart and its tile on the dashboard. It waits for Kater's worker, and answers each place, `added`, `already_there` or `refused` with why.
- **Its conditions.** The query keeps the statement's own conditions in its SQL, and it reads the place's root, so every filter of the place's dashboard narrows it too.
- **One model, or one workspace.** A join or warehouse SQL reads more than one model, so no workspace takes it, and Add to refuses it. Read one shared model, or one workspace, to add it.
- **A saved chart** is a file of its workspace, `charts/<name>.sql`. It is named as a query is, `<workspace>/<name>` or its id (`rel1_…`), never by its file. A chart and a query of one name in a workspace are two objects, and a command about charts reads the chart.
- **Names.** The query is named from its label, else from the statement's title, numbered `_2`, `_3` … when the place has another query under that name, and a new workspace from `--name`, else `unnamed_workspace_<n>`.

Ask before adding anything: a query in a workspace is durable, and a connection workspace's is visible to people with read access.
