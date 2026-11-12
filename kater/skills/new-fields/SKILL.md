---
name: new-fields
description: "New fields. Use it when the person asks for: A new measure, dimension or calculation on a query; A field named in a command, a reference or an answer; How a field is made, or a change to one of its steps."
---

This skill is Kater's guide `vfs://guides/new-fields.md`.

# New fields

How to make a field on a query the way the new-field form does. The form, `kater new field` and you make the same two calls, `field.options` and `field.preview`. You send each one's exact request with `kater field options --request-file -` and `kater field preview --request-file -`, so the field you make is the one the person would have made by clicking. Nothing is saved until the person says yes.

## Where it goes

- **Use what already exists.** Before making a measure or a calculation, search the Catalog (`search_catalog`) for an approved field that already means it, and read its description. If one fits, use it and say which. Make a new one only when none does.
- **Write nothing outside `brainstorm/` before the yes, a workspace or a query included.** A preview saves nothing, so preview first, show it, and then ask both whether to add it and where. Create a workspace or a query only as part of that yes. A plan or a draft goes in your [brainstorm folder](vfs://guides/brainstorm.md).
- **The query the person names.** When they name one ("add it to spend_by_platform"), preview it there. The apply refuses a query they can't change.
- **No query named.** Preview it on a query that already shows what the field is made from; any query they can read will do, since nothing is saved. Then ask where it goes: on that query, when they can change it, or on a query in their private workspace, since a metric you propose is unverified until they confirm it. Make that workspace or query only once they've chosen it.
- **What each layer holds** is in [model layer rules](vfs://guides/model-layer-rules-promotion.md#custom-calculations): only a query holds a custom calculation. A new field goes on a query, not on a workspace root, which selects every field with `*`. A field that several queries already compute goes on the root by `kater model promote`, which points each of them at it ([Promoting a field](vfs://guides/brainstorm.md#promoting-a-field)).

## The steps

`field.options` walks the form's steps and saves nothing. Send the query's workspace as `namespace`, the query as `document` with its full path from `connections/`, and what's chosen so far. `kater field options WORKSPACE --request-file -` sends the workspace that WORKSPACE names, its folder or its id, so the request can leave `namespace` out; written out, it is the workspace's scope and its name in upper case, and a person's workspace's scope is `{"kind": "personal", "connection": "<connection>", "user_id": "<their user id>"}`:

```json
{
  "namespace": {"kind": "workspace", "scope": {"kind": "shared", "connection": "snowflake_prod_connection"}, "workspace": "AD_REPORTING"},
  "document": {"path": "connections/snowflake_prod_connection/workspaces/ad_reporting/query_contracts/spend_by_platform.sql"},
  "kind": "custom_calculation",
  "field": "sum__spend",
  "make": "running_total"
}
```

It answers `step`, the first step left open, with that step's choices in the form's own words, and the answer's `revision` is the query's. Where the chart isn't a file, send the target it is instead of `document`: an unsaved query as `draft_query`, an Explore card as `card`, or a result's Explore recipe as `recipe`.

1. **Start with** (`step: "field"`). `fields` lists what a new field can start with, a metric's first, each with `shown` when the query shows it. Send the one you mean as `field`, by its `name`.
2. **Turn it into** (`step: "recipe"`). `recipes` lists what that field can become, grouped as the form groups them (Over time, Compare with, Against the rest, Row by row), each with a tiny before → after in made-up numbers: Running total `3 5 2 → 3 8 10`. Send its `make`.
3. **Settings** (`step: "settings"`). Each setting has a `label`, one plain `hint`, whether it's `required`, its `fields` or `choices`, and its `value` now. They start where the form starts them: a running total runs along the query's date, or adds one by month when the query shows none (`adds`), and starts over for each of the query's other dimensions. Change one only when the person asked for something else, with `parameters`: a field by its `name`, `{"partition_by": ["platform"]}`, and a choice by its `value`, `{"order_direction": "desc"}`.
4. **Name it** (`step: "naming"`, `complete: true`). The draft is whole. `names` holds what the writer calls it, and `draft` and `inputs` are what `field.preview` takes, as they are.

Send `kind` when you know it: `custom_calculation` for a number worked out from the query's rows, such as a running total, a share or a comparison with another period; `measure` for a new aggregation, or a formula of measures such as one divided by another, which is worked out again at whatever it's grouped by; `dimension`; or `filter`. Left out, the recipe chosen decides. Send every step you already know at once: a `field` and a `make` answer at the settings, or straight at `naming` when every setting has its start.

**Your own SQL**, only for what no recipe writes, such as one measure divided by another. Skip `field.options` and send `field.preview` the draft `{"from": "sql", "expression": "…"}`, with the query's revision from `read_file`'s `sha256`. Write each field with the alias the query's FROM gives its source, and `EVAL(…)` around a measure inside an expression: `EVAL(ad_reporting.sum__spend) / NULLIF(EVAL(ad_reporting.sum__conversions), 0)`. When what you wrote is a call a recipe writes, `reads_as` names the recipe's operation.

## Never ask what you can read, and never invent

- **Don't ask what the steps answer:** the date it runs along, what it starts over for, which way it's ordered. Say what they are when you show it.
- **No name, label or description unless the person gave one.** Leave `naming` out and the writer names it by what it is: "Spend (Running Total)". When the person used words of their own for it, such as "cost per conversion", say the label the writer gives and offer theirs in the same question.
- **No setting the menu doesn't offer,** and no value it didn't list.
- **Compare with a period only through the presets the date offers** (Previous period, Same period last year, 2 periods back). The period is the timeframe the query groups the date by. Never pair a grain with a unit yourself ([calendar comparisons](vfs://guides/period-comparisons.md)).
- **No numbers of your own.** Every figure you show comes from the preview's rows.

## Preview, show, ask

Call `field.preview` with the same `namespace`, the `document` with the `revision` `field.options` answered with, its `kind`, `draft` and `inputs` as they came, and `limit: 10`:

```json
{
  "namespace": {"kind": "workspace", "scope": {"kind": "shared", "connection": "snowflake_prod_connection"}, "workspace": "AD_REPORTING"},
  "document": {"path": "connections/snowflake_prod_connection/workspaces/ad_reporting/query_contracts/spend_by_platform.sql", "revision": "<revision>"},
  "kind": "custom_calculation",
  "draft": "<draft>",
  "inputs": "<inputs>",
  "limit": 10
}
```

It writes the field and runs the query with it, and saves nothing. Its answer through the `kater` tool is a bounded summary: the field, its definition as Markdown, what it adds, and at most ten rows. Show the person five things, in this order:

1. **The label**, and what kind of field it is: "Spend (Running Total), a calculation".
2. **What it is in words:** the definition's Expression and From lines.
3. **The Kater SQL:** `field.expression`, in a code block.
4. **What else it adds to the query:** each of `adds`, such as "Adds Reporting Date by month". An add with a `reason` is a dimension the field's measure cannot be added across: say its `reason` as it is, such as "Currency is added too, because Spend cannot be added across different values of Currency." Say so when it adds nothing. A dimension it adds changes what one row of the query is, so when charts read the query (`chart.list` with `{"source": {"name": "spend_by_platform", "model_type": "query", "namespace": …}}`), say they'll show that too and ask whether to add it here anyway.
5. **The first rows**, as a small table: `rows.columns` in their order, the `new` column marked, up to ten rows.

A field can preview and still carry a warning in `diagnostics`: the compiler saying how it's better made, such as "doesn't depend on the other rows of the result … Declare it as a DIMENSION, or as a MEASURE to reuse it at any grain" for a ratio sent as a calculation. Follow it and preview again before you show anything.

Then say what wasn't shown: "10 of 48 rows" when `rows.more` is set; why there are no rows when `rows_problem` says so, since the field is written either way; a filter's "Keeps 4 of 12 rows" from `kept`. Then ask: "Add it to spend_by_platform?" Nothing is written before the answer.

## Apply on yes

- **Yes:** run `model edit QUERY --path FILE --apply-plan -` with `kater_apply`, with `{"plan": …}` in `stdin`, the preview's `plan` exactly as it came. Don't rebuild it or change its text, and keep its `dependencies_digest`, which stands for the files the query reads.
- **Then say what was saved, and where:** the field's label, the file (`changes[].path` of the answer), and what the write added.
- **If the query or a file it reads changed since the preview,** the apply refuses the plan as stale. Preview again, show what changed, and ask again.
- **No, or something else:** change that step with `field.options` and preview again. Nothing was saved.
- **A target that isn't a file** has no plan. The answer holds it with the field in it, `draft_query`, `card` or `recipe`, which goes back where it came from.

## When it's refused

A refusal is `status: "invalid"`, with `problem` in words for someone who has never written SQL and `problem_at`, the step it's about. Change that step; don't send the same draft again.

- **`field`:** another field to start with. `suggestions` holds the names spelled most like the one sent.
- **`operation`:** another recipe or another setting.
- **`expression`:** your SQL. Each of `diagnostics` is placed in the expression, and its `suggestions` is what to write there instead. "No field is named sum__spnd." suggests `ad_reporting.sum__spend`; "Spend is a measure, so inside an expression it's written EVAL(ad_reporting.sum__spend). Write that here." suggests that.
- **`name`:** the query already has a field of that name. Only a name the person gave can be taken; ask them for another.
- **A held measure.** Some measures can't be added up across a dimension: "Spend can't be added up across Currency. Group by Currency or filter it to one value." That needs the dimension on the query, or a filter to one of its values. Ask the person which; don't choose for them. A running total or a share of such a measure has to keep that dimension in Start over for each, or Out of the total for each (`partition_by`): add it there, since leaving it out would add Spend up across currencies.

## Editing a field

Read `kater field show FIELD` before changing an existing field. FIELD is the field's key, `<model key>.<field>` such as `semantic.orders.revenue` or `query.shop/top_regions."Revenue"`, its id (`fld1_…`) or a `kater://…/fields/<field>` reference, or a query (`<workspace>/<name>`) with `--field NAME`. A bare name folds as the warehouse folds names (on Snowflake `revenue` is `REVENUE`), and a quoted one keeps its case. The answer gives the field's key and id. It gives the same breadcrumbs the field's card and formula bar show: what it starts from, then each step in order. Each of the field's own steps is numbered from 1. A field shown as its source makes it also shows that source's steps; change those in the layer that made them. `kater field edit FIELD --list` shows the line and the choices the field offers.

To change one step, use `kater field edit FIELD --step N --set NAME=VALUE`. To remove it, use the same line with `--remove`. Preview without `--write`, show the breadcrumbs before and after, and apply through `kater_apply` only once the person says yes. The writer checks the step's input and every later step that needs its result, including another field's steps. A refusal names the field and reason; do not replace its SQL to get around it. Add a new step through the field's menu operation on its result, after the last step. There is no reorder or insert in the middle.

To rebuild a field from a recipe, use `field.options` and `editing`, the field's name. It answers with how the field is made now (`editing`: its `make` and Kater SQL) and the settings as they are. Change what the person asked for, `make` or `parameters`, then send `field.preview` the new `draft` with `editing` set to `editing.output`, and apply on yes as above. The field keeps its name, and its place among the fields, when the recipe or the field it starts from changes. A label the writer gave it is written again for what it now is, and a label the person gave it stays. Never rewrite a field's SQL to change its recipe. A field written in its own SQL has no operation steps: change its SQL with `edit_file`, as the [public creation workflow](vfs://guides/public-creation-workflow.md) says. An Excel formula stays a formula and is changed as [Excel formulas](vfs://guides/excel-formulas.md) describes.

A person can unlink a field's SQL from its steps in the form and type their own. `-- @custom` is then written above the field, and the field is never read back as a recipe: `field.options` answers no `make` for it, and the form opens it on its SQL, with a Custom badge. Keep `-- @custom` when you change such a field's SQL. Take it away only when the person asks for the field to go back to the steps.

## Worked examples

Both are on ad_reporting's `spend_by_platform`, which shows Platform, Currency and Spend:

```sql
SELECT
  ad_reporting.platform,
  ad_reporting.union__currency,
  ad_reporting.sum__spend
FROM workspace.ad_reporting AS ad_reporting
GROUP BY
  ad_reporting.platform,
  ad_reporting.union__currency
```

### A running total of Spend

The person asks: "add a running total of spend to spend_by_platform".

1. `field.options` with the query as `document`, `kind: "custom_calculation"`, `field: "sum__spend"` and `make: "running_total"`. Every setting has its start, so it answers `step: "naming"` and `complete: true`: Along is Reporting Date by month, which the query doesn't show yet (`adds`); Start over for each is Platform and Currency; Order is Oldest first. The writer calls it "Spend (Running Total)".
2. `field.preview` with its `draft`, its `inputs` and `limit: 10`.
3. Show it and ask:

   > **Spend (Running Total)**, a calculation: Spend added up month by month, oldest first, starting over for each Platform and Currency.
   >
   > ```sql
   > KATER__WINDOW_AGGREGATION(EVAL(ad_reporting.sum__spend), 'sum', 'running') OVER (
   >   PARTITION BY ad_reporting.platform, ad_reporting.union__currency
   >   ORDER BY KATER__MONTH(ad_reporting.union__date_day) ASC
   > )
   > ```
   >
   > It also adds Reporting Date by month to the query, since a running total needs a date to run along. The query then has a row per Platform, Currency and month, and the two charts that read it, "Spend by platform" and "Spend mix", will too.
   >
   > | Platform | Currency | Reporting Date | Spend (Running Total), new | Spend |
   > | --- | --- | --- | --- | --- |
   > | (the first ten rows) | | | | |
   >
   > Add it to spend_by_platform anyway?

4. On yes, the apply with the preview's `plan`, then: "Added Spend (Running Total) to spend_by_platform.sql, with Reporting Date by month."

### Spend per Conversion

The person asks: "add cost per conversion".

1. Search the Catalog. Ad Reporting has Spend and Conversions and nothing for cost per conversion, so it's a new, unverified field.
2. No query is named, so preview it on one that shows what it's made from: `spend_by_platform` shows Spend, and the preview adds Conversions to its rows. No recipe divides one measure by another, so it's SQL, and a formula of measures is a measure, which stays right by campaign or by month: `field.preview` with `kind: "measure"`, the query's revision from `read_file`, and the draft `{"from": "sql", "expression": "EVAL(ad_reporting.sum__spend) / NULLIF(EVAL(ad_reporting.sum__conversions), 0)"}`. `NULLIF` leaves a platform with no conversions empty rather than failing.
3. The writer calls it "Spend per Conversions", a measure that needs one Currency per row, as Spend does. Its definition reads "From: Spend, Conversions", it adds nothing to the query, and the rows show Spend and Conversions beside it (`made_from`). Show the five things, say that it's labelled "Spend per Conversions" and offer "Cost per Conversion" instead. Then ask where it goes: on `spend_by_platform`, or on a query in their own workspace, since the definition is theirs until it's confirmed.
4. On their answer, apply the plan for `spend_by_platform`. For their own workspace, create the workspace and a query there that shows Spend by Platform and Currency (`create_workspace`, then `query.new`), and preview again on that query before applying. Had they chosen "Cost per Conversion", preview again with `naming: {"label": "Cost per Conversion"}` first, since the plan writes the label.
