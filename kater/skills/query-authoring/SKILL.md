---
name: query-authoring
description: "The rules for every query and workspace root you create or change. Read it before the first edit of a query."
---

This skill is Kater's guide `vfs://guides/query-authoring.md`.

# Query authoring

These are rules, not advice. Follow them in order for every query you create or change.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

A workspace root follows the same rules, with two differences: every field you add to it is a definition its queries read, so it has a name, and a root runs no window. Change a root as [configure workspace](vfs://guides/configure-workspace.md) says.

An answer in a chat is a query like any other, in one of the person's own workspaces that reads the fields, made with create_workspace on the shared model when none does, and the answer cites that query. A scratch statement in your brainstorm folder is only for trying a field you are unsure of, and no answer cites it. It follows the same rules, with one difference: its picks go to `sandbox.statement.write` instead of `query.new`, as vfs://guides/sandbox-scratch-files.md says under Writing a statement. A query in a brainstorm copy is a query like any other, with the copy's full path as `WORKSPACE`.

A saved query that other people read, through a dashboard's tile, an app's chart, a For you card or a chart in a chat, never changes to try what one person asks, such as "this by quarter". Anyone who can read the chart can try a change to it: never refuse because they can read the workspace and not change it. Open their draft of it with `kater query sandbox`, change its picks there, and keep it only through Save as a duplicate (`kater scratch duplicate`, their own copy of the workspace), Save as a new query, Replace the original after the list of what reads it, or Send to the owner, as vfs://guides/sandbox-changing-a-chart.md says under Changing a chart someone else made.

## 1. Load the contract

1. Read vfs://guides/query-contract.md and vfs://guides/public-creation-workflow.md.
2. List what a new query can pick, in one call. It reads what the last compile stored, and saves nothing.

   ```
   kater query fields WORKSPACE
   ```

   It lists each field by the name a pick takes, under a menu of the operations the field offers. Each operation's parameters are one signature, such as `timeframes.set(unit: year|quarter|month|week|day)`. For a query that reads another query (rule 6), add `--source <workspace>/<first>`. To list fewer fields, add `--search WORDS`, `--kind measure` or `--from MODEL`.
3. Ask for one operation only when a parameter chooses a field, or when a call fails. In a pick, a field parameter, such as `by` or `partition_by`, names a field the query picks. On a saved query, `kater operation list FILE --field FIELD --operation ID` lists the fields that each field parameter takes, the other parameters, and the `target` for `edit.model`.

## 2. Pick fields. Do not write SQL.

For a saved query, first read `kater model show query.WORKSPACE/NAME` for what it is made of and `kater field show WORKSPACE/NAME --field FIELD` for a field's breadcrumbs. They are the same cards and steps the person reads in Kater. A field made with operations keeps every recognized step from its SQL; hand-written arithmetic is SQL as a whole, and an Excel formula stays its formula. A source's steps belong to its own layer. To change one of this query's numbered steps, preview `kater field edit WORKSPACE/NAME --field FIELD --step N --set NAME=VALUE`, or `--remove`, then add `--write` only when the person has approved the change. A later step that needs it is refused with the affected field and reason. Add a new operation to the field's result after its last step.

4. Make the query with `query.new` (`kater query new WORKSPACE --request-file -`, which sends the workspace that WORKSPACE names as the request's `namespace`): the fields by name, at most one `operation` from each field's menu, `conditions`, `group`, `order` and `limit`. For example:

   ```json
   {"fields": [
      {"field": "region"},
      {"field": "order_date", "operation": "timeframes.set", "parameters": {"unit": "month"}},
      {"field": "sum__amount", "operation": "comparisons.previous_period", "parameters": {"by": "order_date", "output": "percent_change"}}
    ],
    "conditions": [{"field": "order_date", "operation": "is_within_last", "parameters": {"amount": 12, "unit": "month", "complete": true}}],
    "order": [{"field": "sum__amount", "descending": true}],
    "limit": 100}
   ```

   With no `order`, the compiler orders the query: its first date, newest first, else its first measure, largest first, else its first dimension, A to Z. So a `limit` always keeps the same rows, and you need no `order` for an Excel formula that reads another row. Such a formula reads the rows in the query's order, and only the rows the query returns, after its `limit`. A query cannot sort by a formula that reads another row: sort by another column.

   Add `"required": true` to a condition when the answer depends on its window. Then no reader can turn it off or change its value. Do not add it to a window on the date that a comparison reads. A comparison moves an optional window to the earlier period, but it does not move a required one, so the earlier value is null and the compiler warns with `comparison_outside_required_filter`. A query's own optional filter that an operation writes starts on (`on=TRUE` in its call), so the window applies unless a reader switches it off for themselves.

   A field that shows the measure a reader selects with a parameter, as a Tableau Selected Measure does, is a pick too: `{"field": "sum__spend", "operation": "derive.measure_choice", "parameters": {"parameter": "trend_measure", "choices": [{"value": "Spend", "field": "sum__spend"}, {"value": "Clicks", "field": "sum__clicks"}]}, "output": {"name": "selected_measure", "label": "Selected Measure"}}`. Each choice is a number measure, and the field is verified when each measure is (vfs://guides/parameters.md). Never write it as `DECODE` or `CASE`.

   Leave `label` out unless the person asks for a title. A query with no `label` shows Kater's words for its fields and its date window, which are written again when the query changes, and a chart with no `label` of its own takes its query's title. A title the person asks for goes through `label.set`, which renames the query too; an empty one gives the title back to Kater and keeps the name. Kater's words never rename a query or a chart.
5. Change a filter of the saved query with `kater filter`, never in the SQL: `kater filter list QUERY` shows each one, `kater field values QUERY FIELD` lists a field's real values, and `kater filter add`, `change`, `set` and `remove` write as the filter pill does (vfs://guides/filters.md). Run each line without `--write` first, which shows the change and what it reaches. Never write `OPTIONAL_FILTER`, `REQUIRED_FILTER` or a `KATER__` condition by hand. Add a step to the saved query with the field's operation through `edit.model`: aggregations, comparisons, windows, calculate_at, derive, filter_by_this_number, timeframes, sort and format. Run rule 3's command with `--json`, then send `[{"target", "operation_id", "parameters"}]` to `kater model edit QUERY --path FILE --operations-file - --write`. Use the `target` whose `kind` is `active`, the field the query shows. A field parameter takes the field's reference from the answer's `field_choices`, not its name.
6. **No CTEs. No subqueries.** A step that needs its own SELECT is its own query. Make it first. Then make the second query with `query.new` and `source: query.<first>`. Its fields, conditions and order name the first query's fields. It cannot read a field that the first query shows with no name, so name that field in the first query (rule 11). To move a CTE that a query already has, run `kater model edit QUERY --path FILE --extract-stage CTE --write` (`query.extract_stage`). Add `--stage-name NAME` to give the new query a name that says what it holds. Each CTE it reads moves too. When a CTE must stay, the result says why. A CTE whose `EVAL` changes a measure's context, with `REMOVE`, `SET` or `CLEAR`, stays: as a query of its own its numbers can change. A Tableau FIXED LOD that a dimension reads is one, as vfs://guides/migrate-tableau-workbook.md says.
7. Write Kater SQL only when no operation makes the change. Before you write it, say which operation you tried and why it could not.

## 3. If you write Kater SQL

8. **Use the `KATER__` function when one exists.** `KATER__MONTH`, not `DATE_TRUNC`. `KATER__PREVIOUS_PERIOD` and `KATER__PERCENT_CHANGE`, not a self-join or `SUM(IFF(month = …))`. `KATER__RANK`, `KATER__WINDOW_AGGREGATION` and `KATER__LAG`, not `RANK()` or `SUM() OVER`. `KATER__IS_GREATER_THAN` and `KATER__IS_IN_TOP` for conditions. A filter is a call from vfs://guides/filters.md, not a bare WHERE. Where your SQL computes what a date's grain or a window function does, `kater model inspect FILE` gives the hint `kater_function_available`. Apply its fix with `kater model edit QUERY --path FILE --fix ID --write`.
9. **Select a field bare.** `a.spend` passes the measure through. Write `EVAL(a.spend)` only inside an expression, or in HAVING, QUALIFY or ORDER BY.
10. **Do not name a field.** Write `expression AS "Label"`. An unnamed output is correct, and the compiler sets its type from its formula. Keep a name the writer generates, such as `running_sum__revenue`.
11. **Name a field only when a later query reads it, or it is a definition to reuse.** Then:
    - A reader must compute it again at its own grouping, such as a ratio of measures: a named `MEASURE`.
    - A later query groups or filters by it, such as a bucket, a label or a flag: a named `DIMENSION`.
    - A later query reads a window or a calendar comparison as a finished value: a named `CUSTOM_CALCULATION`.

    A value computed from a measure is never a `DIMENSION`. Never give a source field a second name, such as `DIMENSION('currency', a.currency)`: select the field. The compiler warns on both, with `measure_value_declared_as_dimension` and `dimension_renames_field`. Apply the fix unless a later query reads the field. In `query.new`, set `reusable` only for a field a reader must compute again at its own grouping.
12. **One name, one formula, across the workspace.** A name says what, from what, and how: `spend_change_vs_previous_month`, not `change`. Its label says the same in words. The words after an `AS` are always the label; with no `AS`, the field keeps the label of the field it reads, and the name shows only when no layer has a label. A label never reads as another field's name (`label_reads_as_other_field`): a number shown with words, such as Week 1, is a format of the number (`-- @format "Week " plain`, rule 13), not a field of its own; a text that no format can write is named `<name>_label`, never `<name>_number`. Every write says when a field computes what another field of the workspace computes (`repeated_formula`), or uses a name that another field uses for another formula (`name_reused`). If the formula already exists, select that field: pass it through, or `EVAL` it inside an expression. Never declare it again under any name. If the name exists with another formula, choose another name.
13. **Do not format in SQL.** No `TO_CHAR`, no `CASE` and no `'Week ' ||` for display. Use `format.display`, whose words go around a number (`-- @format "Week " plain`), and `derive.map`. A value map that every query needs goes in the shared model.

## 4. Finish

14. Read every write result. Fix every error. Fix as many warnings about a wrapper, a name or a repeated formula as you can, and say which ones you leave and why.
15. Compile with `kater compile --select WORKSPACE`. Then run once with `kater run --select <workspace>/<name>` and check the rows before you call it done.

## Stop conditions

- No operation and no rule here covers the change: stop and ask.
- A name you need exists with another formula, and you cannot tell which one is correct: stop and ask.

## Keys in filters

Read the field's contract `key`, not its name or `@format id`. A key takes an ID condition, with pasted or typed IDs; it has no range or comparisons. `kater field values QUERY FIELD` on a large key reads no value list and tells you to give the IDs with `--is-one-of`. A small key whose sync sample counted at most 1,000 values can also list them. To take a file as the ID box takes a paste, use `kater filter add QUERY FIELD --is-one-of --from-file ids.txt` (new lines, commas, spaces and tabs separate IDs; repeats are kept once). Review the proposal, then use `--write`. The same check refuses a value no row holds; use `--allow-unseen` only when the person wants to keep it. A query or workspace cannot declare a key: write it on the dataset the refusal names, following vfs://guides/dataset__kater.md.
