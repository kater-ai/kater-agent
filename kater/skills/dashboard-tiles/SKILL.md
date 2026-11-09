---
name: dashboard-tiles
description: "Dashboard Reference: make each tile answer one question. Use it when the person asks for: A saved chart of a query, as a dashboard's tile or an app's chart draws it."
---

This skill is Kater's guide `vfs://guides/dashboard-tiles.md`.

# Dashboard Reference: make each tile answer one question

A part of [Dashboard Reference](vfs://guides/dashboard.md).

## Make each tile answer one question

A dashboard reads well when each tile draws the few figures that answer one question. A query can return more columns than its chart draws: the others stay in the tooltip, the table and `kater show`. Never bind every column of a query to its chart. That is what makes a dashboard a wall of cards and panels.

| The query returns | Draw it as |
| --- | --- |
| One row of 2 to 4 headline figures | `stats`, each figure with its `prior`: the column it is read against, such as last week's. |
| One row of more figures, or figures that tell one story, such as a forecast with its low and high case | `insight`: a sentence that quotes 3 to 5 of them. |
| Rows along a date | `line`, or `bar` for an amount in each period. The other category column, such as the platform, is `series`. |
| Rows by a category, such as a platform or a campaign | `bar` with one or two figures. A current and a prior figure side by side is `bar` with `stacking: grouped`. A figure on another scale, such as a ratio beside amounts, is `composed` with that figure on `axis: right`. |
| Rows to read across many columns, such as the top campaigns | `table` with `tableVariant: report`, its `columns` the ones a person reads. |

Make a query's chart with `kater new chart NAME --source-query <workspace>/<query> --draw-file DRAW_FILE`, which makes it drawn in one command, and change a chart with `kater chart draw <workspace>/<chart> --request-file DRAW_FILE`. Both take the same draw file, and `-` reads it from stdin. It gives `draw`: the kind, and each role as the SQL name of a column the query returns, and `formats` beside it. Add `"dry_run": true` to check it and save nothing. A wrong name is refused with every column the query returns. A format for a column the chart does not draw is left out. The answer's `problems` say what makes the chart hard to read on each dashboard that shows it ([What the check reports](vfs://guides/dashboard-look.md#what-the-check-reports)). A draw file for a Bars and line chart:

```json
{
  "draw": {
    "kind": "composed",
    "category": "WEEK",
    "values": ["SUM__SPEND", "ROAS"],
    "value_settings": [
      {"label": "Spend", "mark": "bar"},
      {"label": "ROAS", "mark": "line", "axis": "right"}
    ],
    "presentation": {"legend": true}
  },
  "formats": {"SUM__SPEND": "currency('USD', 0)", "ROAS": "number(2)"}
}
```

`kater chart describe <workspace>/<query> --kind KIND` lists the roles and settings of a kind, each setting with where it goes (`presentation` or `value_settings`), what it takes and what it does.

The roles are `category`, `values`, `series`, `value`, `rank`, `comparison`, `percentage` and `columns`. `title` in `draw` writes the chart's `label`, so give it only when the person asks for a title. Each value's own settings go in `value_settings`, one entry for each of its values in order, as [the Sandbox guide](vfs://guides/sandbox-charts.md) lists them: a Bars and line chart's `mark` and `axis`, a value's own `label`, or a stat card's `prior`, the column it is read against. Give `formats`, beside `draw`, to show a column in a format of its own without changing its query, written as a `-- @format` is: `{"draw": {"kind": "stats", "values": ["SPEND_LATEST_THREE_MONTHS"]}, "formats": {"SPEND_LATEST_THREE_MONTHS": "currency('USD', 0)"}}`. A chart split into `series` gets a legend, and a pie or a breakdown reads its largest part first. A chart draws its rows in its query's order, and a date axis runs oldest to newest: to put bars in another order, change the query's ORDER BY, as a person who sorts the chart in the portal does. The slices of a `series` stack, and read in the legend, in the order the rows first show them: to set their order, give `presentation.seriesOrder` the series' values from the axis out, such as `{"series": "CAMPAIGN_PHASE", "presentation": {"stacking": "stacked", "seriesOrder": ["Launch week", "After launch week"]}}`, which puts Launch week at the bottom of each bar and first in the legend. A value that the list does not name comes after the named ones. A person sets the same order in the chart's Settings, under Legend order. A Text chart (`insight`) gives `template`, its words in Markdown with a `<kater-value field="COLUMN"></kater-value>` tag for each figure, and the columns its tags name are its values: `{"draw": {"kind": "insight", "template": "<kater-value field=\"SPEND\"></kater-value> of ad spend brought **<kater-value field=\"REVENUE\"></kater-value>** of revenue."}}`. Its words may use **bold** and *italic*, and nothing else of Markdown.

- **A row of cards holds about one figure for each 3 columns of its tile:** `stats` holds 4 in a 12-column tile and `metrics` holds 5, and each holds 2 in a 6-column one. More figures wrap to another row, and Kater makes a new tile tall enough for its rows.
- **Use `multiples` only for 2 to 4 figures in different units over one axis.** Each figure gets its own panel, so 9 figures make 9 panels.
- **Leave a chart's title to Kater.** With no `label`, a chart's title is Kater's words for what it shows, and they change when its query changes. A chart with no `label` of its own takes its query's title. Write a top-level `label` beside the chart's `name` only when the person asks for a title: it stays as written.
- **Put tiles side by side.** An 8-column chart beside a 4-column one, or two of 6, reads better than a column of full-width tiles. When a person asks for a dashboard to look better, group its tiles into sections, one for each question it answers.

## The tooltip

A chart's tooltip shows its own fields when nobody sets it. Use it for the figures a reader needs about one mark that the chart does not draw, such as the orders behind a revenue bar, so the chart itself stays one question. `tooltip` in the draw file adds at most 6 fields after the chart's own, each by the SQL name of a column the query returns: `bar`, `line`, `area`, `composed`, `pie`, `radar`, `peak`, `ranked`, `multiples`, `treemap`, `sunburst` and `sankey` take it. A treemap or a sunburst shows the added fields on a part that is one row, not on a part that holds many. A dimension gives `{"column": ..., "distinct": ...}`, where `distinct` is the column that counts its values in each mark, so a mark that holds more than one shows "3 values". A tooltip field never groups the query: give the query the column first, a measure as it is, or a dimension as its MIN and its COUNT DISTINCT, with `kater model edit`. `presentation.tooltip` sets the rest: `hide` lists the chart's own fields that the tooltip leaves out, `labels` renames a field in the tooltip only, an added field's too, and `text` puts words above the rows, in Markdown with a `<kater-value field="COLUMN"></kater-value>` tag for each figure. `"presentation": {"tooltip": false}` shows no tooltip. `kater chart describe <workspace>/<query> --kind KIND` names the setting and its limit.

```json
{"draw": {"kind": "bar", "category": "REGION", "values": ["SUM__REVENUE"], "tooltip": ["COUNT_DISTINCT__ORDER_ID"], "presentation": {"tooltip": {"labels": {"COUNT_DISTINCT__ORDER_ID": "Orders"}, "text": "**<kater-value field=\"COUNT_DISTINCT__ORDER_ID\"></kater-value>** orders"}}}}
```

A person sets the same in the chart's settings, under Tooltip.
