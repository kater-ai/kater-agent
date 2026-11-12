---
name: pin-and-alert
description: "Pin, alerts and markers: what a person does with a number they care about. Use it when the person asks for: \"Tell me when ROAS drops below 3\", \"pin this\", \"draw a line at 3\": a chart's Alert me when, Pin or Add marker."
---

This skill is Kater's guide `vfs://guides/pin-and-alert.md`.

# Pin, alerts and markers: what a person does with a number they care about

A chart offers four things, and each does one thing. Say what each does, and use the one the person means.

| What the person says | On the chart | What it does | The command |
| --- | --- | --- | --- |
| "Draw a line at 3", "mark the day we launched" | Add marker | Draws a line. It tells nobody. | `kater chart marker add` |
| "Keep this on my Home", "pin this" | Pin | Keeps a chart or a figure in Pinned on their Home, with its value current. | `kater pin add` |
| "Tell me when ROAS drops below 3" | Alert me when | Checks the figure each night and tells them. | `kater alert add` |
| "Add this to the project" | Add to | Puts it in a project, a dashboard, an app or a workflow. | `kater add-to`, `kater project metric add` |

A figure is one field at one row of a query, written `<field>[:<row key>]`, such as `platform_roas:TikTok Ads`. A query is its workspace's path and its name.

Each command sends what the page sends, so the person sees the result on the chart, on Home and on the Alerts page at once. A pin and an alert are kept in Kater, in no file. A marker for everyone is in the chart's file ([Markers](#markers)).

## Markers

A marker is a horizontal line at a value or a vertical line at a date.

```text
kater chart marker list QUERY [--chart NAME]
kater chart marker add QUERY --chart NAME --value platform_roas=3 --label "Break even"
kater chart marker add QUERY --chart NAME --day 2026-10-05 --label "Launch"
kater chart marker change QUERY MARKER --chart NAME --value platform_roas=3.5
kater chart marker remove QUERY MARKER --chart NAME
```

- **Who sees it.** A person who can edit the chart writes it in the chart's file, for everyone. Anyone else draws one only for themselves. `who` in the answer says which: `everyone`, `only_me` or `project`. Say it to the person.
- **The `kater` tool** writes a person's own marker at once. A marker for everyone commits to the chart's file, so `kater` refuses it, and the same line runs through `kater_apply` after the person says yes.
- **A project's target** is a line with `who: project`. It changes in the project, with `kater project metric set PROJECT NAME --reach`.
- **Removing a marker** leaves an alert that followed it at its value.

The chart's file holds its markers under `chart:`, in `markers:`. Change them with these commands. A hand edit keeps the same shape, with at most 32 lines:

```yaml
chart:
  markers:
  - {id: mk_break_even, on: value, field: platform_roas, value: 3, label: Break even}
  - {id: mk_launch, on: period, day: 2026-10-05, label: Launch}
```

A marker changes how the chart looks, so it never runs the warehouse.

## Pins

```text
kater pin list [--query QUERY]
kater pin add QUERY --figure platform_roas:TikTok Ads --label ROAS
kater pin add QUERY --chart NAME
kater pin remove QUERY --figure platform_roas:TikTok Ads
```

Only the person sees their pins, so the `kater` tool runs these with no question. Pinning an object, such as a dashboard or an app, is its own Pin on its page, and it shows in the same Pinned. A pin never tells anyone. Each figure answers `pending` until it is read: say so, and do not run a query to check it.

A figure's values are the query's data, so a pin of a figure and an alert need execute access on the query, as a run does. Without it, the refusal is "You don't have execute access to this query or source.", and `kater pin list` and `kater alert list` leave out the figures of each query the person cannot run. Taking out a pin or an alert needs no execute access.

## Alerts

```text
kater alert recommend QUERY --figure platform_roas:TikTok Ads [--chart NAME]
kater alert add QUERY --figure platform_roas:TikTok Ads [--when below:2.2] [--better higher] [--tell-in slack]
kater alert add QUERY --every [--when change:20]
kater alert list [--query QUERY]
kater alert change ALERT --when below:2.5
kater alert remove ALERT
```

- **What it starts on.** `--when` is `below:<value>`, `above:<value>`, `change:<percent>` or `normal`, for outside its normal range. `--better` is `higher`, `lower` or `none`. What you leave out takes the recommended value: a marker on the figure first, else the value worked out from its weeks. `kater alert recommend` shows that and why. Most people do not know what number to write, so offer the recommendation.
- **What it holds.** The alert holds the values it was set with. They do not change by themselves. A level from a marker follows it: a move of either moves the other.
- **Where it tells them.** Every alert is a row of their Inbox. `--tell-in` adds `slack`, `email` or `brief`, once for each place, and `inbox` alone means the Inbox only. That is the person's choice, so never pick it for them: left out, it starts from where their newest alert goes. Slack needs the organisation's Slack and their own account connected, and the command says when it does not.
- **When it tells them.** Each alert is checked every night. It tells the person once for each crossing, and one figure speaks once on one morning, also when it is in their project too.
- **Any figure.** `--every` covers each figure of the query. It takes `normal` or `change:<percent>`, since one value cannot fit every figure.
- **The `kater` tool** runs these with no question, since an alert is the person's own. Set one only when they asked for it, and say what it will do. An alert whose level moves a marker for everyone goes through `kater_apply`.

`kater alert list` reads what is stored and runs no query. Each row gives `sentence`, `status` and where it tells them. The person sees the same table at `/alerts`.

## An alert or a watch

A chart's figure takes an alert. It needs no workflow and no model key. A watch (the skill watch) is a workflow for a query that no chart draws, or for a check at a time of the person's own. See [workflows](vfs://guides/workflows.md).

A figure in a project is checked too: a project's owner hears about each figure's alert in their Inbox with no alert of their own. See [projects](vfs://guides/projects.md).
