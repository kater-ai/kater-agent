---
name: filters
description: "Kater SQL filters. Use it when the person asks for: A filter on a query or a workspace, \"only show EUR\", \"start this filter on for everyone\", \"use the same filter in every query\", where a filter lives and what it starts as."
---

This skill is Kater's guide `vfs://guides/filters.md`.

# Kater SQL filters

**Change filters with `kater filter`, never in the SQL.** The commands send the operations the filter pill sends, so the writer writes each call, checks each value against the data and keeps every reader's choices bound. Never write `OPTIONAL_FILTER`, `REQUIRED_FILTER` or a `KATER__` condition by hand. Run each write first without `--write`: it shows the SQL it writes, as a diff, the filter's name and label, and how many queries, charts, dashboards and apps it reaches. Show the person that, and run the same line with `--write` through `kater_apply` once they say yes.

Each command names its query first, as `<workspace>/<name>` (such as `shop/top_regions`) or its id (`rel1_…`), never by its file, or a workspace by its folder.

| To | Run |
| --- | --- |
| See every filter, in one shape | `kater filter list QUERY` (or the workspace), with `--json` for each filter's `name`, `label`, `home`, `required`, `on`, `condition` (`field`, `operation`, `values`), `words`, `operations`, `fixed` and `text`. A filter with `fixed` (`shared_model` or `sql`) offers no `operations`: no caller changes its condition, and a run that sends one is refused |
| See a field's real values | `kater field values QUERY currency`, `--search win` for the values that hold a text, `--bounds` for a number's or a date's lowest and highest |
| Add a filter | `kater filter add QUERY currency --is-one-of USD EUR`, or on the workspace (or with `--workspace` from a query) for every query of the workspace, `--required`, `--off` (with no condition, a filter that starts off keeps every value: it is written as Is not empty, which a reader changes), `--label`, `--name`, `--description` and `--ai-context` |
| Give it another condition or field | `kater filter change QUERY currency --is-not-one-of EUR`, with `--field region` for another field |
| Change its settings | `kater filter set QUERY currency --label 'Order currency' --off`, `--on`, `--required`, `--optional`, `--description`, `--ai-context`, `--name NEW` |
| Split an AND into filters | `kater filter split QUERY big_usd` |
| Make a plain condition a filter | `kater filter name QUERY "shop.channel <> 'store'" --as no_store` |
| Move a query's filter to its workspace | `kater filter move QUERY currency` |
| Remove a filter or a plain condition | `kater filter remove QUERY currency` |
| Run once with another condition | `kater run --select QUERY --filter currency --is-one-of EUR`, or `--filter currency --off`: no file changes. A value no row holds is refused; `--allow-unseen` keeps it |

**A condition is one flag**, named by the condition: `--is-one-of V…`, `--is-not-one-of V…`, `--is-equal-to V`, `--is-at-least V`, `--is-between LOW HIGH`, `--contains TEXT`, `--is-empty`, `--is-within-last N UNIT [--complete]`, `--is-on-or-after DATE` and the rest that `kater filter add --help` lists. `kater filter list` gives the flags each filter's field takes. A date takes a date, such as `2026-01-31`, or a relative date in words, so the filter moves with each day: `today`, `now`, `'3 months ago'`, `'3 complete months ago'`, `'1 week from today'`, `'the start of this month'`.

**Use real values.** Take each value from `kater field values` first. A value that no row holds is refused with the values near it, such as "No row has Currency usd. USD is near it.". Add `--allow-unseen` only for a value the person says the data will hold later.

**A filter written as SQL** is never overwritten. `kater filter change` refuses it with what to do: `kater filter split` for an AND of conditions, or a change in the SQL editor by the person. A plain `WHERE` condition shows in `kater filter list` as `kind: where`, always on: `kater filter name` makes it a filter that a reader sees.

The rest of this page is how filters work, and how a file reads after the commands write it.

Kater optional filters are named predicates owned by a workspace root or a query. Shared Kater datasets and semantic models may own parameter-free required filters. The compiler validates each definition under its own policy before expanding it into a consumer; a query cannot legalize a shared parameter or optional filter. See [parameters](vfs://guides/parameters.md) for input types, defaults, and constraints.

This page is the reference for reading a filter. Every change goes through `kater filter`, as [Public creation workflow](vfs://guides/public-creation-workflow.md) says.

## Predicate rules

| SQL | Parameters | Behavior |
| --- | --- | --- |
| `OPTIONAL_FILTER('name', predicate, label='…')` | Any, including none | Optional; workspace/query only |
| `REQUIRED_FILTER('name', predicate, label='…')` | None | Always applied |
| Ordinary WHERE/HAVING/QUALIFY, join, or conditional predicate | None | Ordinary SQL behavior |
| Ordinary predicate containing a Kater parameter | One or more | Rejected; use a supported optional filter placement |

A filter operation writes its predicate as a `KATER__` call: the operation's id in upper case, the field, then the operation's values. `KATER__IS_ONE_OF(region, 'EMEA', 'AMER')`, `KATER__IS_BETWEEN(amount, 10, 100)` and `KATER__IS_WITHIN_LAST(date_day, 30, 'day')` are three. The compiler lowers each call to the SQL it stands for, and the input bar reads the call back into its controls. It reads the SQL a call lowers to the same way, so `region IN ('EMEA', 'AMER')` opens as Is one of. Any other predicate, such as a comparison of two fields, is ordinary SQL: it compiles, and the input bar shows it as its SQL. A date condition takes a relative date as its value, or as either end of a range, so the filter moves with each day: `KATER__IS_ON_OR_AFTER(order_date, KATER__AGO(3, 'month'))` and `KATER__IS_BETWEEN(order_date, KATER__AGO(3, 'month', complete => TRUE), KATER__TODAY())`. The relative dates are `KATER__TODAY()`, `KATER__NOW()`, `KATER__AGO(n, unit)` and `KATER__AHEAD(n, unit)`, and `KATER__MONTH(KATER__TODAY())` is the first day of this month. Never write the dates that a window means today. In a shared model, a filter operation writes the call alone in WHERE, such as `WHERE KATER__IS_EQUAL_TO(o.region, 'west')`, and it always applies.

Both named functions must be direct AND conjuncts of WHERE. Parentheses around a conjunct are allowed. They cannot appear under OR or NOT, in HAVING/QUALIFY/JOIN ON, inside CASE/measure conditions, or nested in one another. Unwrapped Kater parameters in these conditions are also rejected. The compiler reports the authored owner, parameter, and source span; it does not move or wrap a predicate automatically because doing so can change its logic or aggregation grain.

The label is optional and always the third argument, written `label='…'`. A third argument of any other shape fails with `invalid_filter_arguments`. The label is the condition in words, and the filter bar names its pill with it. So write it as a short sentence about the rows, such as `label='First touch is known'`. A definition card lists an always-applied filter's condition as its SQL under Always filtered.

These rules govern Kater constructs. The warehouse's own variables, bindings, predicates, and scripting in a custom SQL dataset follow the custom SQL execution contract; they are not declarations of Kater inputs. `FILTER(array, lambda)` is the warehouse's own array function, in Snowflake and DuckDB alike.

## Optional and required filters

This workspace root has three filters, as the filter operations write them. Every filter has its values in its call: the writer never declares a parameter for a field's filter. The amount filter always applies. The caller switches the region filter and the channel filter, and each caller can change their operation and their values on the page they are on.

```sql
---
name: sales
model_type: workspace
---
SELECT
  *
FROM kater_dataset.sales AS sales
WHERE
  -- @description Restrict sales to the selected regions.
  -- @ai_context Use for geographic comparisons.
  OPTIONAL_FILTER('region', KATER__IS_ONE_OF(region, 'AMER', 'EMEA'), label='Region', on=TRUE)
  AND REQUIRED_FILTER(
    'amount',
    KATER__IS_GREATER_THAN(amount, 0),
    label='Amount'
  )
  AND OPTIONAL_FILTER(
    'channel',
    KATER__IS_EQUAL_TO(channel, 'paid'),
    label='Channel'
  )
```

A new filter is named by its field, such as `channel`, with `_2`, `_3` and so on when the name is taken, and labelled with its field's label: the pill shows the condition beside it. A set of values is written once each, in order, with at least one value and at most 1,000. A relative date is written as Kater's date functions, such as `KATER__IS_BETWEEN(order_date, KATER__AGO(3, 'month'), KATER__TODAY())`, so it moves with the day the query runs. An operation's target can give the name, the label, the description, the AI context, Required and On by default in the same write (`name`, `label`, `description`, `ai_context`, `required`, `on`). Every query that reads the root gets these filters.

An optional filter's default is in its own call: `on=TRUE` starts it on for a reader who chose nothing for it and for every job, and without `on=` it starts off, at every layer. Above, the region filter starts on and the channel filter starts off. A reader's own switch wins over the default on the page they are on. No other file switches a filter: a dashboard keeps no `filters:`. A new optional filter that an operation writes starts on unless its target sends `on: false`, and a required filter made optional starts on, so its rows stay. `kater filter set --on` or `--off` writes `on=TRUE` or takes it out, in place. A required filter is always on and takes no `on=`.

**Where a choice lives.** The file holds each filter's default: its condition and `on=`. Each reader's switch and condition are run-time choices, kept for them on the page they made them on (the query's page, a dashboard or an app), and they change no file. A chart keeps no filters: its query's file and the page it is on decide them. So to change what everyone gets, change the file (`kater filter set --on` or `--off`, or `kater filter change`); to change what one person sees, they switch the pill on their page. To answer for other values once, `kater run --select QUERY --filter NAME` takes a switch or a condition for that run alone.

**What a run says.** `kater run` takes the person's choices for the query's page (a chart's run takes its query's page), unless the request names a `consumer`, its own `inputs` or a `--filter`. Each result's `filters` lists every filter of the query in the shape `kater filter list` gives, as the run held it: `on` is whether it narrowed the rows, `condition` and `words` the condition that held, `set_by` (`file`, `person` or `run`) who decided the switch, `condition_set_by` who decided the condition, and `written` the file's condition when the run's is not. The terminal prints the required filters, then each optional filter that is off as its file starts it, and each choice that is not the file's, such as "Big orders is off for you.". When you quote a figure, say each filter that was on in words, and say when a filter was off or its condition was not the file's for this person. Never call a figure everyone's when its run took a person's choice.

A filter the operation adds on a query's own field is an optional filter of the query, with its value in its call:

```sql
---
name: orders_by_status
model_type: query
---
SELECT
  sales.region,
  sales.amount,
  sales.status
FROM workspace.sales AS sales
WHERE
  OPTIONAL_FILTER(
    'status',
    KATER__IS_EQUAL_TO(sales.status, 'shipped'),
    label='Status',
    on=TRUE
  )
```

A change to a filter keeps its name, so every reader's choices and every place that names it still bind. `kater filter change` writes the new condition, on the same field or another, where the filter stands, with its comments, and keeps a label someone wrote. A filter its author wrote as the plain SQL of one condition, such as `region = 'EMEA'`, is written as its `KATER__` call, and the command says so. A filter written as other SQL is never overwritten: the change is refused with `filter_written_as_sql`. `kater filter split` writes a filter whose SQL joins conditions with AND as one filter for each condition, and `kater filter name` makes a plain WHERE condition a required filter named by its field. The new field's type decides the operations it takes. A filter that read a parameter gets its values written in, and the parameter goes when nothing else reads it. `kater filter set` changes the label, the description, the AI context and the switch, and with `--required` or `--optional` writes the call again as a `REQUIRED_FILTER` or an `OPTIONAL_FILTER`, in place. `kater filter set --name` gives it a new name in every file of the workspace that names it.

A filter's value is static when its predicate reads no parameter and parameterized when it reads one or more. Every filter an operation writes is static. A filter its author writes as other SQL, such as a week that a reader selects, may read a parameter its author declares. A required filter is always static: a parameterized required filter fails with `parameterized_required_filter`, even when every parameter has a default, and `kater filter set --required` refuses to make one required. Moving a pre-aggregation shared predicate to an outer query may change results; compare the results deliberately before migrating authored content.

Write a shared model's required filter with `edit.model`'s `clauses.required_filters` (`kater model edit MODEL --path FILE --clauses JSON`): `{kind: add, predicate, label?, name?}` over a condition you write, or `{kind: add, field, operation_id, parameters}` with a `filters.*` operation from that field's menu, as a field's Always filter writes it; `{kind: remove, name}` takes one out. The writer writes `REQUIRED_FILTER('name', condition, label='…')` as a condition of WHERE, on its own line, and names it by its field and labels it with the field's label when they're left out. A measure can't be filtered this way: filter by a value each row has.

## Moving a filter

A filter has one home: the workspace root or one query. It moves up only, from a query to its workspace root, with `kater filter move QUERY NAME` first, which shows what moves and what the warehouse check finds, then the same line with `--write` through `kater_apply` once the person says yes. The root gains the filter as it is, with its default, and every other query's copy of the same condition goes, under any name, unless that query's rows need it; the dry run lists each copy. Nothing moves a workspace filter down, since every other query would lose it. To ask the data team for a filter in a shared model, send a promotion request (`kater promotion request PATH --filter NAME --reason "…"`, [model layer rules](vfs://guides/model-layer-rules-promotion.md#promotion)).

## Activation and binding

| Caller input | Result |
| --- | --- |
| Filter inactive; its exclusive parameter omitted and no default | No value required; positional value is null and activation is false |
| Filter active; required value omitted and no default | Input error before any warehouse dispatch |
| Filter active; valid value supplied or defaulted | Predicate applies |
| Invalid value supplied, including for an inactive filter | Input error; supplied values are always validated |
| Parameter used by several optional filters | Required whenever any selected filter uses it |
| Parameter also used in a non-filter expression | Required regardless of optional filter selection, unless a default supplies it |

An optional filter does not make a parameter nullable. Missing input never silently disables an active filter. A parameter's default supplies a value but selects no filter: the call's `on=TRUE` does. Required filters need no activation slot and remain active whether or not their names are explicitly selected. Unknown input names and unknown filter names are rejected.

Run compiles the selected models once, binds the entire batch before its first execution, and retains compiled SQL and diagnostics if binding fails. Chained queries inherit filters and parameter requirements. Chart snapshots and execution requests carry both parameters and selected filters; changing either changes their identity.

## Labels, descriptions, and AI context

A filter says everything about itself where it is written. Nothing about a filter goes in the frontmatter. `kater filter set` writes all three (`--label`, `--description`, `--ai-context`).

| Where | Meaning |
| --- | --- |
| `label='…'`, the call's third argument | Optional nonblank display label |
| `-- @description …` directly above the filter | Optional nonblank human explanation |
| `-- @ai_context …` directly above the filter | Optional nonblank guidance for an agent |

"Directly above" means on the lines just before the filter, or just before its `AND`, with no blank line between. A directive may also follow the closing parenthesis on the same line. `/* @description … */` works for text that spans lines. A label written as a comment directive above a filter fails with `filter_label_directive`: its label is the call's `label='…'`; any other directive fails with `unsupported_filter_metadata`, and a directive given twice with `duplicate_filter_metadata`.

## Inheritance

Filters with the same name in reached definitions share one activation slot and combine their parameter uses and owners. A name cannot be both optional and required. Every call of one name writes one condition, or compilation fails with `conflicting_filter_condition`: write the same condition, or give the second filter its own name. When several of them give a label, description, or AI context, the values must agree, or compilation fails with `conflicting_filter_metadata`.

A filter is known by its home and its condition (the field, the operation and the values), not only by its name. A query that writes the condition a model it reads already writes, such as its workspace root, under any name, gets the warning `repeated_filter` ("West repeats the workspace's filter West only. It changes no rows."). Its quick fix, Use the workspace filter only, takes the query's copy out of its `WHERE`. The copy changes no rows when the workspace's filter is required or has the same name; when the workspace's is optional under another name, the copy holds while a reader has it off, and the warning says so. Do not write a workspace filter again in a query: read it from the workspace. The contract lists each copy (`copies` on the filter), and the filter bar draws one pill with a 2 for the two. Unreached models contribute no inputs. Local CTEs retain the declaring model's ownership. Parameter metadata inherits by dependency ancestry; see the parameter reference for conflict rules.

For migrated repository windows, defaults and result-sensitive filter moves, see [Model migration and explicit query inputs](vfs://guides/model-migration.md).

Filter predicates and EVAL modifiers bind declared field identifiers, never AS display labels. Static contracts preserve symbolic conditions, owner/stage, requiredness, and activation rules; runtime evidence records selected filters and resolved inputs. Defaults do not activate optional filters. EVAL context changes are isolated to that occurrence and cannot remove defining restrictions.

## Keys filter as IDs

A field whose contract has `key` takes Is one of, Is not one of, Is equal to, Is not equal to, Is empty and Is not empty, regardless of its categorical or continuous class. It offers no comparison or range. Its conditions have `values: typed`, and its value parameter uses the ID box: paste or type IDs separated by new lines, commas, spaces or tabs. Repeated IDs are kept once. Is equal to and Is not equal to take one ID.

Opening the ID box reads no list or bounds for a key. The exception is a synced column whose sample counted at most 1,000 distinct values: its box also offers that list. Add checks only the IDs given with one `WHERE ... IN (...)` read. Missing IDs are marked, with a count such as “3 of 4 found”; keep them only with the person's choice, or the CLI's `--allow-unseen`.

`kater field values QUERY order_id` on a large key says “Order ID is a key, so no values are listed for it. Give the IDs with --is-one-of.” It reports the sync's sampled count when one exists. `kater filter add QUERY order_id --is-one-of --from-file ids.txt` reads IDs with the same separators as the box, shows the proposed SQL and its reach, and writes only with `--write`. `--from-file` also works with `--is-not-one-of` and for a field that is no key. It refuses an empty or unreadable UTF-8 file before it reads a service or warehouse.

`kater filter list QUERY --json` carries each key field's DatasetKey in its `condition.key`, with the kind and dataset. The filter's own `values` is `typed` for a key; the condition's `values` is the actual list the filter holds. A plain WHERE condition's identity key stays separate.
