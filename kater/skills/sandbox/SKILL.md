---
name: sandbox
description: "Sandbox. Use it when the person asks for: A number, a trend, a comparison, a quick answer; \"Is that the number the team sees?\", a figure checked against what other people read; A field you are unsure of, to try before a query uses it."
---

This skill is Kater's guide `vfs://guides/sandbox.md`.

# Sandbox

The Sandbox is where a person tries something without choosing a workspace, a model or a layer first. Make statements in a scratch file, against any shared semantic model or Kater dataset, one of your workspaces, or the warehouse, and run them with `kater run --select FILE`. A statement is made the way a query is, from picks ([Writing a statement](vfs://guides/sandbox-scratch-files.md#writing-a-statement)). When an answer is worth keeping, add its statement to a workspace.

**An agent answers from a workspace, not from here.** An answer in a chat is a query in one of the person's own workspaces, written with `kater query new` and run, so each figure it cites is a saved query of governed fields ([A figure over a period](#a-figure-over-a-period)). An agent writes a scratch statement only to try a field it is unsure of, and never quotes or cites one.

## A statement in three calls

Each statement is written from picks, never typed. This is the whole path for one statement over a shared model:

1. **Write the picks** with `kater scratch statement.write --request-file -`. The picks go inside `picks`. What the statement reads goes beside them: a shared semantic model or Kater dataset as a typed `source`, or one of your workspaces as `workspace`, by its folder. Your own folder is `connection.folder` of `kater_start`.

   ```json
   {"namespace": {"kind": "scratch", "connection": "<connection>", "user_id": "<your user id>"},
    "path": "connections/<connection>/users/<your email>/brainstorm/<topic>/checks.sql",
    "source": {"name": "ad_reporting", "model_type": "semantic", "namespace": {"kind": "shared", "connection": "<connection>"}},
    "picks": {"fields": [{"field": "platform"}, {"field": "sum__spend"}],
              "conditions": [{"field": "union__date_day", "operation": "is_between", "parameters": {"range": ["2026-07-01", "2026-07-31"]}}],
              "order": [{"field": "sum__spend", "descending": true}]}}
   ```

   The scratch `namespace` names you by your `user_id`, which is `person.user_id` of `kater_start`; your email names only your folder in `path`. It saves nothing. Its answer's `sql` is the statement, and its `parameters` are what the statement's conditions read.
2. **Put it in the file** with `kater scratch append --request-file -`: `{"connection": "<connection>", "path": "<the same path>", "title": "July 2026 spend by platform", "sql": "<the answer's sql>", "parameters": <the answer's parameters>}`. A file that isn't saved yet is made.
3. **Run it** with `kater run --select <the path>`.

`picks` takes `fields`, `conditions`, `group`, `order` and `limit`, with the names `query.new` uses. `kater query fields WORKSPACE` lists the fields you can pick. [Conditions](vfs://guides/sandbox-scratch-files.md#conditions) shows each condition's `parameters`.

## A figure over a period

A question such as "How much did we spend on ads last week?" is a lookup. Answer it with one run, and give three figures, never the figure alone: the newest complete period, the period before it and the change. A person who sees a figure asks next why it moved, and the period before is what that answer starts from.

Write it as a query in one of the person's own workspaces that reads the measure, with `kater query fields WORKSPACE`, then `kater query new WORKSPACE --request-file -` with the picks below and no `namespace`: the command sends the workspace that WORKSPACE names. When none of their workspaces reads it, make one on the shared model that holds it with create_workspace. The query is saved at once and changes nothing that exists, so do not ask first.

- **Group by the period,** with `timeframes.set` on the date (`unit: week` for a week), and keep only the newest complete one with an `is_within_last` condition with `amount: 1`, `unit` the period, `complete: true` and `end: latest`, so a source that runs behind today still gives its newest full period.
- **Add the period before and the change** as two picks of the measure with `comparisons.previous_period`, `by` the date, one with `output: value` and one with `output: percent_change`. The comparison moves the window back by itself, so do not widen the condition.
- **Say which period it is,** with its dates, as "the newest full week in your data". When the period the person named is not complete or not in the data yet, such as this week, say so in one line, and never give a part of a period as the whole.
- **Add the main split as a second query,** such as spend by platform with the same picks, and run both in one `kater run --select QUERY QUERY`. The person sees the parts too.
- **Say what every figure keeps.** `kater query fields` and `kater run` name the required filters of what a query reads, such as "Currency is USD", and a measure whose field `holds` a dimension, such as one Currency for each row, is split by it. Name each in the answer in words, such as "in US dollars", so the person knows what the figure counts. A run takes the person's own choices for the query's page, and each result's `filters` says which: say each one that is not the file's, such as "with Big orders off, as you keep it", so they know the figure is theirs and not what everyone sees ([filters](vfs://guides/filters.md)).
- **Say through which day a period runs** when it is the newest one and not complete, such as "October 1 to 19". Get the last day in the data from a query of its own with one pick, the date with `aggregations.max` and no period: a pick of the date beside a period gives one last day for each period.

Answer with no plan and no question, such as: "The newest full week in your data is Oct 5 to 11: [$61,117](app_url) on ads, up [17.8%](app_url) from [$51,872](app_url) the week before. The week of Oct 12 is not in yet." Quote each figure as the result gives it: never work out the change yourself. Make each figure a link with the `app_url` its run gives, and end with its source, the query as a link with its `query_url`, as Kater's instructions say.

## The same number the team sees

A question such as "Is that the number the rest of the team sees?" needs two checks, not a read of the definition alone.

1. Explain the measure with `explain_field`. Note each rule it gives, such as "Required: one Currency per result row", and each required filter.
2. Read one team workspace that uses the measure: find it with `search_catalog` or `grep` in the connection's `workspaces/`, and read its root's required filters with `kater query fields WORKSPACE`. Run one of its queries over the same period, or add one there with the same picks, and compare one total with yours.

Say what you compared: the definition, the filters and the total. When the totals differ, say which filter or rule makes the difference, and never call two figures the same from their definitions alone.

## The parts

Read the part for what you do:

- **Write a scratch file and its statements:** [scratch files and statements](vfs://guides/sandbox-scratch-files.md).
- **Run a statement, read a workspace, use warehouse SQL, or keep a statement as a test:** [running statements](vfs://guides/sandbox-running.md).
- **Chart a statement, or add it to a dashboard:** [charts](vfs://guides/sandbox-charts.md).
- **Try a change to a chart that someone else made:** [changing a chart someone else made](vfs://guides/sandbox-changing-a-chart.md).
