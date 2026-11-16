---
name: sandbox-charts
description: "Sandbox: charts. Use it when the person asks for: A saved chart of a query, as a dashboard's tile or an app's chart draws it."
---

This skill is Kater's guide `vfs://guides/sandbox-charts.md`.

# Sandbox: charts

A part of [Sandbox](vfs://guides/sandbox.md).

## Charts

A chart belongs to a statement that reads one model, on the `query` route, or one workspace, on the `workspace` route. The Sandbox draws it with the chart shell, the same one Explore, the editor and dashboards use: its pills change the statement, and its type and settings change the chart. It is kept in the file's `charts:` frontmatter, one entry per charted statement, so it is there the next time the file opens:

```yaml
charts:
- statement: 0
  fingerprint: "410042cb42e1"
  chart: {version: 2, kind: bar, type: axis, title: Spend by platform, category: platform, values: [sum__spend], series: union__currency, presentation: {orientation: vertical}}
```

- **`statement` and `fingerprint`** are the index and fingerprint a run returns in its `statement`. The fingerprint lets the entry find its statement again after statements move. Quote it, since one that is all digits would read as a number. A new entry can leave `fingerprint` out: the first save gives it the fingerprint of the statement it names.
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
| `metrics`, `stats` | Metric row, Stat cards | `values`, and optionally a percent-change `comparison`. One row, or one row for each date: a second row for a date, or more than one row and no date, is refused. Over more than one date, the figures are the latest date's, and each draws every date under it as a sparkline, which `presentation.sparkline: false` hides |
| `table` | Table | `columns`; left out, every output. Its other roles and its settings are listed [below](#a-tables-roles-and-settings) |
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

`title` and `description` are the reader's own, and `presentation` holds the chart's whole style, in the chart spec's own keys, such as `orientation`, `stacking` or `legend`. No key is added to it: a `bar` with no `orientation` stands upright, so a chart opened in the Sandbox and saved with no change saves as it was. `stacked: true` is short for `stacking: stacked` on a `bar`, `area` or `composed` chart. A key the kind does not take is refused, with the key's name.

**Formats.** `formats` gives a column the chart draws its own number format, by the column's name, written as a `-- @format` is: `formats: {sum__spend: "currency('USD', 0)"}`. A currency is a code, or a column the statement returns that holds each row's code, like `currency(currency_code, 0)`, and a format for a column the chart does not draw is left out. A column with none shows its query field's format. A chart saved from a tile keeps the formats its tile showed.

**The tooltip.** With no setting, a chart's tooltip shows its own fields. `tooltip` adds at most 6 fields after them, each by its output's name: `bar`, `line`, `area`, `composed`, `pie`, `radar`, `peak`, `ranked`, `multiples`, `treemap`, `sunburst` and `sankey` take it. A treemap or a sunburst shows the added fields on a part that is one row, not on a part that holds many. A dimension gives its output and `distinct`, the output that counts its values in each mark, so a mark that holds more than one shows "3 values". `presentation.tooltip` changes the rest: `hide` lists the chart's own fields that the tooltip leaves out, `labels` renames a field in the tooltip only, an added field's too, and `text` puts words above the rows, in Markdown with a `<kater-value field="…"></kater-value>` tag for each figure, as an `insight`'s template does. `presentation.tooltip: false` shows no tooltip. A field that the statement does not return, or a seventh field, is refused:

```yaml
  chart: {version: 2, kind: bar, category: region, values: [sum__revenue], tooltip: [count_distinct__order_id], presentation: {tooltip: {labels: {count_distinct__order_id: Orders}, text: "**<kater-value field=\"count_distinct__order_id\"></kater-value>** orders"}}}
```

**A tooltip field never groups.** A field added to the tooltip is never in the GROUP BY, so it never splits a mark or changes the chart's grain. A measure is worked out at each mark's groups, as the chart's own values are. A dimension is read as one value for each mark, with its MIN, MAX and COUNT DISTINCT: the tooltip shows the value when the mark has one, and how many values it has when it has more. This holds for every chart kind, and a `sankey`'s `details` are tooltip fields. Vega-Lite and Tableau's Detail shelf group by a detail field, which splits each mark; a tooltip field here never does.

**A chart follows its statement.** Every save re-keys `charts:`, whether the file is saved from the Sandbox, VS Code, `write_file`, `edit_file` or `source.write`:

- **The same fingerprint at the same index:** kept.
- **The same fingerprint at another index:** moved there, so inserting a statement above a charted one moves its chart down.
- **Neither:** the statement was edited, so the entry keeps following the lines it was on, and takes the statement's new fingerprint.
- **A deleted statement:** its entry is kept past the last statement, detached. It finds its statement again if a statement with its fingerprint comes back.
- **No fingerprint:** a new entry takes the fingerprint of the statement it names.

**A draft of a query keeps its chart here too.** Each change that the line above a chart in Sandbox mode names makes the person's draft. A chart type or the chart's settings, such as one value's mark or side, change no SQL and run nothing, so they go in the draft's `charts:`, and a draft that changed only those holds the query's own SQL. A setting changed together with a field goes there too, and a field added with nothing else keeps no chart. Opening the draft draws that chart again (`query.draft` answers it as `chart`), and taking the change back removes the entry. A filter switched off is the reader's choice, and the file keeps none of it. [Changing a chart someone else made](vfs://guides/sandbox-changing-a-chart.md) says what each Save does with the chart.

**Its rows.** Dates run in order; otherwise the statement's own ORDER BY is kept, and categories run in order without one. The statement's LIMIT is the chart's, so "Top 20" appears in the title only when the statement asked for it.

**Changing a statement from its chart.** Each pill change is the statement's picks written again, as the field picker writes a query from the same picks: `sandbox.statement.read` reads a statement as its picks, with the fields and menus its chart offers, and `sandbox.statement.write` returns the statement's new text for the picks, which the Sandbox puts where the statement is, as typing does, with the file's `parameters:` as its `parameters` say (see [Conditions](vfs://guides/sandbox-scratch-files.md#conditions)). Switching a condition off writes nothing. A statement with SQL no pick makes, such as `WHERE amount > 10`, keeps its fields; change them in its SQL, and `sandbox.statement.read` says why in `unread`. A statement that reads a workspace, or one of its queries as a draft does, reads as the picks of the relation it selects from: that query's outputs, with their menus. A next question, or a chart type set up, opens as a new scratch file, in a tab of its own.

**Drawing one without the UI.** `kater scratch chart.preview --request-file -`, with the scratch file's `namespace` (written as in step 2 of [Writing a statement](vfs://guides/sandbox-scratch-files.md#writing-a-statement)), `path`, `statement` and, optionally, the `chart` to draw, `inputs`, with `filters` for the optional filters to switch on and `off` for those to switch off, and `limit`, at most how many rows (10,000 by default). It returns every output the statement has, planned for that chart, whose footer's finding it words; without one, or when it no longer fits the statement's outputs, the rows are the statement's own, as a table. From a terminal: `kater scratch chart.preview --path FILE --statement N`, with `--chart '{"version": 2, "kind": "bar", "category": "platform", "values": ["sum__spend"]}'` to try one.

**A chart's columns are its query's fields.** A saved chart's SELECT names each column as the query does, `q.region`, and a binding in its encodings names the same field: `output: region`. The words after an `AS` are only the column's header: `q.region AS "Area"` is the column `region` headed Area, and a binding of `area` is refused. Write an `AS` only for a header that the query's own label for the field does not give. A second column of one field takes its name from its `AS`.

### A table's roles and settings

<!-- table-settings:start -->
<!-- Generated by scripts/generate_table_guide.py from packages/model-contracts/src/kater_model_contracts/chart_components.json. Do not edit: run pnpm generate:table-guide. -->

A table chart is `kind: table` and nothing else: a table has no layout to name, and one with no setting draws its columns as they come. A setting left out has the default below, the same in a chart file, a tag and the chart's Settings, so a file holds only what it changes. Grouping rows is `encodings.groupBy`, one column: "group it by region" writes that and no other key. A column is a SQL name the query returns. Every other key of `encodings` is the role's own.

| Key | Role | Takes | What it does |
| --- | --- | --- | --- |
| `columns` | Columns | a list of columns, in order | The columns to show, in this order. Empty means every column the contract returned. |
| `encodings.groupBy` | Group by | one column | Folds the rows under each value of the column, with its count. Offered: a column whose values repeat: at least 2 different values, each held by 2 rows or more on average. |
| `encodings.detail` | Row details | a list of columns, in order | The columns a row opens to show, which leave the row. |
| `encodings.identity` | Name and picture | one column | The column that names each row, shown with a picture beside it. |
| `encodings.status` | Status badge | one column | The column drawn as a coloured badge on each row. |
| `encodings.segments` | Tabs | one column | One tab for each value of the column, which takes a column of few values. Offered: a column whose values repeat: at least 2 different values, each held by 2 rows or more on average, with at most 8 different values. |
| `encodings.avatar` | Picture | one column | The column that holds each row's picture, as an address. It goes beside the name. |

`kater chart execute` answers `snapshot.columnValues`: for each column, by its `name` in `snapshot.columns` (its `sqlReference` is the SQL name), how many different values it holds over every row and whether Group by, Tabs and Filter offer it (`offers`). Choose a column from the ones that offer the setting: Group by a column that does not repeat makes a group for each row.

| `presentation` key | Takes | Default | What it does |
| --- | --- | --- | --- |
| `density` | comfortable or compact | `comfortable` | How much space a table's rows take. |
| `tableHeight` | a whole number from 160 to 1200 | not set | A table's height in pixels. |
| `freezeColumns` | a whole number from 0 to 32 | `0` | How many of a table's first columns stay in view while it scrolls sideways. |
| `tableRowNumbers` | true or false | off | Numbers a table's rows. |
| `pageSize` | a whole number from 5 to 100 | not set | How many rows a table shows on one page. Left out, every row is on one page. |
| `stickyHeader` | true or false | on | Keeps a table's headings in view while it scrolls. |
| `tableStriped` | true or false | off | Shades every second row of a table. |
| `tableBars` | a list of text | the first 2 figures | A table's columns that draw a bar before each value, by output name. Left out, its first 2 figures do. |
| `tableSearch` | true or false | off | Shows a search box over a table. |
| `interactive` | true or false | on | Lets a reader hover, click and sort the chart. false draws it still. |
| `tableSelectable` | true or false | off | Lets a reader select a table's rows. |
| `tableFilter` | true or false | off | Lets a reader filter a table's rows by the values of its columns. Offered: a column whose values repeat: at least 2 different values, each held by 2 rows or more on average, with at most 24 different values. |
| `tableColumnPicker` | true or false | off | Lets a reader show and hide a table's columns. |
| `tableCollapsible` | true or false | on | Lets a reader close and open a table's groups. |
<!-- table-settings:end -->

### A table's totals

A table's Total row and Total column are picks of its query, not settings of the chart, so the chart file holds no key for them and every table of the query draws them. Each is one operation on the query as a whole: `kater operation list QUERY --operation totals.table` for the Total row, and `--operation totals.column` for the Total column, each with `--json`. The answer's `query` list says whether it is `offered` or `disabled` with the reason, and holds the `target` to send.

```sh
kater model edit QUERY --path FILE --operations-file - --write   # through kater_apply
# stdin: [{"target": TARGET, "operation_id": "totals.table", "parameters": {"on": true}}]
```

Send `on: false` to turn it off. The query's own writer chooses the figures; no second query is needed.

- **A Total row counts the rows the table shows.** Beside top rows, a row limit or a filter on a number, a figure's total is the total of the rows that are left, and a ratio's total is the ratio of those totals. A figure that cannot follow the rows, such as a previous period of a ratio, is listed in the entry's `left_out` with its reason and has no total. A query that joins and keeps only some of its rows is refused with its reason.
- **A Total column adds within each row**, so a cut leaves it alone. Its `figures` parameter lists every figure the column adds, at least two; sent again, the list replaces the figures it held. A field's own menu also lists `totals.row_total`, which adds a column of its own to the query, with its `fields`, `name` and `label` in place of `figures`; a query with a cut has no such entry, so use `totals.column`.

## Adding a statement to dashboards

A statement becomes a query in a workspace, where stakeholders can reuse it, through Add to, the same Add to as every chart's. `kater add-to FILE --statement N` lists where it can go, and the same line with `--to` adds it, through `kater_apply` once the person agrees where.

- **It is a draft.** A statement has no file of its own, so Add to writes it as a query in each place first, with the parameters its file declares for it. What it reads comes from the file, and so does the chart the file's `charts:` keeps for the statement. `--no-chart` adds the query alone, and `--label` is a title the person wrote for the query and its chart, which names them. Without it neither has a label, so both show Kater's words, and the query is named from the statement's title.
- **Where it can go.** `kater add-to FILE --statement N` lists every place on its Dashboards tab: the workspace it reads, when it reads one, then each workspace over the same shared model you can change, each with its `fit` (`fits`, `pending` while it is checked, `refused` with its reason, or `other_model`, with its reason, for a workspace that reads another shared model or none, which only `--search` shows), the model's Explore page (`explore`) and where a new workspace would go (the tab's `new`). The fits are kept by the statement's text, so a second read of the same text answers at once.
- **Adding.** `kater add-to FILE --statement N --to workspace:<folder> --to new:dashboards --name "<name>"`, through `kater_apply`, adds to all of them in one commit: the query, and with a chart, the chart and its tile on the dashboard. It waits for a background job, and answers each place, `added`, `already_there` or `refused` with why.
- **Its conditions.** The query keeps the statement's own conditions in its SQL, and it reads the place's root, so every filter of the place's dashboard narrows it too.
- **One model, or one workspace.** A join or warehouse SQL reads more than one model, so no workspace takes it, and Add to refuses it. Read one shared model, or one workspace, to add it.
- **A saved chart** is a file of its workspace, `charts/<name>.sql`. It is named as a query is, `<workspace>/<name>` or its id (`rel1_…`), never by its file. A chart and a query of one name in a workspace are two objects, and a command about charts reads the chart.
- **Names.** The query is named from its label, else from the statement's title, numbered `_2`, `_3` … when the place has another query under that name, and a new workspace from `--name`, else `unnamed_workspace_<n>`.

Ask before adding anything: a query in a workspace is durable, and a connection workspace's is visible to people with read access.
