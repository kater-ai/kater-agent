---
name: query-contract
description: "Query contracts. Use it when the person asks for: \"Save it\", \"so I can get the same numbers again\"."
---

This skill is Kater's guide `vfs://guides/query-contract.md`.

# Query contracts

A saved query belongs to its complete workspace path, including connection, verified/personal location, owner, and nested workspace name. Query identity adds its model name. Same-named models under two users are distinct. The compiler model contract is compiler-owned; edit SQL rather than a derived interface.

Save a query with `publish_query_contract`. A `kater` command, a tool and an answer name a query as `<workspace>/<name>`: its workspace's folder from the content root and its name, such as `connections/<connection>/users/<email>/workspaces/<workspace>/<name>`, or by its id (`rel1_…`). A mention names it by `<workspace>/<name>` alone. Never name a query by its file: a file of `query_contracts/` can hold several queries and is not named after them. `list_accessible_workspaces` gives each query's `<workspace>/<name>` and id, and `kater query list <workspace>` gives the file that declares each one (`path`).

Build a query through its operations, listed under [Public creation workflow](vfs://guides/public-creation-workflow.md): write it from the workspace's fields with `query.new` (`kater query new`), or from another query's fields with its `source`, or create it with `kater new query`, then add fields, filters and calculations with `edit.model`. `create_projection` writes a field over an expression you write: with no `name`, as `expression AS "Label"`, which takes the type its formula has; with a name, as a dimension, a measure or a custom calculation, such as the `segment` below. `field.declare` names an unnamed output. This page is the reference for the SQL they write, and for what no operation expresses.

## Final call sites

| SQL | Role and identity | Local → exported type |
| --- | --- | --- |
| `MEASURE('rate', EVAL(s.refunds) / NULLIF(EVAL(s.revenue), 0)) AS "Refund rate"` | Declaration with query-owned key and linked dependencies. | measure → measure |
| `DIMENSION('segment', s.region || ':' || s.channel) AS "Segment"` | Declaration with query-owned key. | dimension → dimension |
| `CUSTOM_CALCULATION('revenue_rank', RANK() OVER (ORDER BY EVAL(s.revenue) DESC)) AS "Revenue rank"` | Declaration with query-owned key, computed over this query's result rows. | custom_calculation → custom_calculation |
| `s.revenue` or `s.revenue AS "Revenue"` | Pass-through: the same measure, keeping the source measure key, with this query's context added. | measure → measure |
| `EVAL(s.revenue)` | Evaluation preserving the source measure key and its own output occurrence. | measure → dimension |
| `s.region` or `EVAL(s.region)` | Evaluation preserving the source dimension key. | dimension → dimension |
| `s.refunds / NULLIF(s.revenue, 0) AS "Refund rate"` or `s.region || s.channel` | Unnamed, with its formula's type; AS never invents a key. | dimension → dimension |
| `RANK() OVER (ORDER BY EVAL(s.revenue) DESC) AS revenue_rank` | Unnamed custom calculation. | custom_calculation → custom_calculation |

The roles are exactly declaration, evaluation, and custom_calculation; the last marks an unnamed output, whatever its type. Field types are exactly measure, dimension, and custom_calculation, and the formula decides which, not the wrapper. A custom calculation is a value computed over this query's result rows: a window, or anything built from one. A wrapper that disagrees with its formula gets a warning with a fix, and a `MEASURE` over a window is an error; see [windows and custom calculations](vfs://guides/window.md). One format or timeframe step around a single field keeps its identity ([dimensions](vfs://guides/dimension.md)); repeated operands, multi-input calculations, CASE, arithmetic and other transformations do not invent one. Invalid SQL, unresolved references, cycles, and illegal stages are diagnostics, never custom fallbacks.

```sql
SELECT
  MEASURE('sum__amount', KATER__SUM(s.amount)) AS "Revenue recipe",
  EVAL(sum__amount) - EVAL(s.refunds) AS "Net revenue"
FROM workspace.sales AS s
```

Sibling declarations bind before evaluation. A value computed from a measure is not a grouping key, so it has no `DIMENSION` name: `DIMENSION('revenue_value', EVAL(sum__amount))` gets the warning `measure_value_declared_as_dimension`. Each EVAL has an independent context. Do not select the same named identity twice as an ambiguous persisted interface; differing labels cannot fix that collision. Distinct named fields may share a label.

```sql
WITH rates AS (
  SELECT EVAL(s.refunds, CLEAR()) / NULLIF(EVAL(s.revenue), 0) AS ratio
  FROM workspace.sales AS s
)
SELECT MEASURE('refund_rate', ratio) AS "Refund rate" FROM rates
```

Transparent CTE reuse preserves the formula, linked inputs, and CLEAR on only the numerator. Unsupported relational stages fail explicitly; see [measures](vfs://guides/measure.md). Anonymous fields can be used by their own result grid and parent-query chart using exact compilation/ordinal OutputRefs. Analytical cross-model selection requires naming them first. Live anonymous chart bindings require reselection after recompilation.

A CTE is better as a query of its own, which other queries can read too. Each CTE that can move gets the hint `cte_can_be_query`. Its fix, or `edit.model`'s `extract_stage` (`kater model edit QUERY --path FILE --extract-stage CTE`), writes the CTE as a query before this one in the same file, and this query reads it as `query.<name>`. Each CTE it reads moves first. A column this query reads keeps its name: a pass-through is read by its field's name, and a value the CTE computes is declared under its column name, as a `CUSTOM_CALCULATION` for a window and a `DIMENSION` for anything else. A CTE stays when it is recursive, when a subquery reads it, or when it changes a measure's context, as the CLEAR above does, since its numbers could change.

In a workspace, one name means one formula, and a formula is defined one time. Every write of a query or a workspace root compares its fields with the root's and with the other queries' fields, and `kater compile` and the editor do the same. A field that computes what another field computes gets the warning `repeated_formula`. When the other field is in the root, select it: the warning names it, and an unnamed output gets a fix that selects it. Otherwise, add the formula to the root one time and read it from there. A name that another field uses for another formula gets the warning `name_reused`: give the field a name that says how it is different. A pass-through, an `EVAL` of one field, a window and a calendar comparison read a field or compute over this query's rows, so they never repeat a formula. Their names still count.

## Editing and Sources

The compiler derives each field's breadcrumbs from the saved SQL: its start, every value step, then a filter by this number, sort and display format. It publishes the same facts for a menu write and the same SQL written by hand. A value with hand-written arithmetic or a function no operation writes is SQL as a whole; an Excel formula keeps its formula. A field read unchanged from its source shows the source's steps in that source's layer. Read and change the numbered steps with `kater field show` and `kater field edit --step`, as [new fields](vfs://guides/new-fields.md#editing-a-field) describes.

Editing consumes local role/type, current settings, and exact occurrence/configuration targets. Sources consumes exported facts narrowed to the active consumer. A month-to-week configuration edit replaces the recognized truncation step around its original timestamp. Formatting to SQL text changes exported type; display formatting changes only presentation. A monthly upstream value cannot recover daily detail. Aggregate, derive, window and calculate-at operations append a declaration under a generated name, and a window's is a `CUSTOM_CALCULATION`; sort, filter, and Filter by this number change clauses. Every operation uses the shared operation ID, narrowed parameter schema, and revision-checked prepare/apply transaction. Unavailable or partially understood structure exposes only proven actions.

## Filter by this number and Top N

A number measure in a query, one it reads from its source or one it declares, offers `filter_by_this_number`: a fixed condition on its value, written in HAVING, or Top N or Bottom N, written in QUALIFY. A SELECT with either can't pass a measure through, so the same edit evaluates every measure the SELECT passes through, keeping its name and label, and groups by the SELECT's row values when it has no GROUP BY. A HAVING or QUALIFY written by hand beside a measure passed through, such as `HAVING ad_reporting.sum__spend > 100000`, reads that measure the same way, with the warning `measure_evaluated_beside_clause` and a fix that writes its `EVAL`. "Regions with over $1,000 in sales", then Top N on the same query:

```sql
SELECT
  sales.region,
  EVAL(sales.revenue)
FROM workspace.sales AS sales
GROUP BY
  sales.region
HAVING
  KATER__IS_GREATER_THAN(EVAL(sales.revenue), 1000)
QUALIFY
  KATER__IS_IN_TOP(EVAL(sales.revenue), 10)
```

Top N and Bottom N keep ties, so the top 10 can hold more than ten rows. A query with no ORDER BY gets one by the measure's place in the SELECT, `ORDER BY 2 DESC NULLS LAST` here for Top N and `ASC NULLS LAST` for Bottom N, so the rows come out in the order they rank; a query that already sorts keeps its own sort.

Top N and Bottom N take `partition_by`, the query's fields they rank within, written as their OVER: `KATER__IS_IN_TOP(EVAL(sales.revenue), 10) OVER (PARTITION BY sales.currency)` keeps the top 10 regions of each currency, so a measure that can't be added up across currencies is never ranked across them. Applying the same condition to the same measure again changes it in place, so Top 10 applied as Top 5 keeps the top 5. `edit.model`'s `workspace_inputs` take one out with `remove_condition`, by its clause and its place there as the input bar lists it; once no HAVING or QUALIFY condition is left, a measure a window of the query reads as it's passed through is passed through again.

A number custom calculation the query computes offers the six fixed conditions, never Top N or Bottom N, since ranking it would run one window inside another. The condition goes in QUALIFY, which applies after the window runs, and the same edit writes the `EVAL`s. "Revenue (Rank) is at most 3":

```sql
SELECT
  sales.region,
  EVAL(sales.revenue),
  CUSTOM_CALCULATION('rank__revenue', KATER__RANK(sales.revenue, 'desc') OVER ()) AS "Revenue (Rank)"
FROM workspace.sales AS sales
GROUP BY
  sales.region
QUALIFY
  KATER__IS_AT_MOST(KATER__RANK(sales.revenue, 'desc') OVER (), 3)
```

## Reading another query's custom calculations

A query that reads another query gets each custom calculation as the value that query computed. Nothing is computed again at the reader's grouping, and the reader's WHERE filters the finished values. With `query.revenue_by_region` publishing `revenue_rank` and `running_revenue`:

| Written in the reader | Result |
| --- | --- |
| `q.revenue_rank`, in a SELECT that doesn't group | the same custom calculation |
| `q.revenue_rank` in a grouped SELECT, or `GROUP BY q.revenue_rank` | a dimension, a grouping key |
| `WHERE q.revenue_rank <= 3`, or an optional filter on it | a filter on the finished values |
| `RANK() OVER (ORDER BY q.running_revenue DESC)` | a new custom calculation |
| `MIN`, `MAX` or `COUNT` of it | an aggregate of the finished values |
| `SUM` or `AVG` of it | allowed, with the warning `custom_calculation_reaggregated`, since a running total summed again counts rows more than once |
| `EVAL(q.revenue_rank)` | an error, `custom_calculation_evaluated`, with the fix "Select revenue_rank without EVAL" |

The field picker inserts one bare, never with `EVAL`. A reader's menu for it is a dimension's menu of its data type, without aggregations. Beside a measure from the same source, the measure reads the value from the source's own result, matched on the source's GROUP BY keys.

## Static and runtime contracts

Static contracts contain symbolic definitions, dependencies, query stages, input requirements, local/exported availability, and exact ordinal execution bindings. Run never rewrites them. Separate ignored `.kater/runtime/results/` records commit typed rows and runtime evidence together: actual/defaulted inputs, selected filters, per-EVAL restrictions, pinned definition snapshots, and completeness. Defaults do not activate optional filters; required restrictions remain enforced.

Local chart previews consume an exact parent result and its OutputRefs. Full-data chart execution uses one bound input context and records that execution without claiming a complete parent rowset. Charts/apps reload compatible durable records after TTL, eviction, or restart through their governed data path. Missing, stale, corrupt, swapped, or foreign metadata reports unavailable; it never substitutes another run or silently executes the warehouse.

Labels are presentation only. A rename (`kater rename NAME NEW_NAME`, then the same line with `--write` through `kater_apply`) changes the field's name and every place that names it, in every folder, in one change, and keeps its label; a column the SQL makes with no declaration is declared under the new name. The old name stays with the object it named: no other field of the model, and no other query, chart or workspace in its place, takes it, so what still names it finds the renamed one. Only a rename back gives the object its old name again. `kater references FIELD --relation MODEL` lists the models and fields that read a field. Repository migrations must review and update the complete dependency chain. Obsolete type spellings are rejected; no compatibility readers or migration-on-read exist.
