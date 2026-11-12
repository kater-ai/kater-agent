---
name: dataset-custom-sql-kater
description: "Custom SQL Kater dataset. Use it when the person asks for: A shared dataset that shapes warehouse tables, or custom SQL."
---

This skill is Kater's guide `vfs://guides/dataset_custom_sql__kater.md`.

# Custom SQL Kater dataset

Read `kater model show kater_dataset.NAME` for its card. From starts with the SQL mark, which shows the custom SQL, then the database models it reads. Fields lists its dimensions and measures, each opening its field card with its own breadcrumbs. The card describes the dataset's parts without reading its warehouse rows.

Set `custom_sql: true` for one read-only query in the warehouse's own SQL, with CTEs and unions if you need them. A script, a statement that changes data and a native binding are refused with `sql_dataset_query_required`. In a person's own terminal, `kater new kater-dataset --custom-sql` writes the file and its starter body. Through the Kater MCP, write the whole file with `kater source write connections/<connection>/modeling/kater_objects/datasets/NAME.sql --text-file -` through `kater_apply`, after the person says yes. The SQL after that is yours to write. A write compiles it like any other, but leaves its text as you wrote it. Every final output requires `DIMENSION('name', expression)` or `MEASURE('name', expression)`. CTE aliases remain ordinary SQL. AS supplies a label and never creates a public name. Opaque tabular publication, final stars, and unwrapped final outputs fail with guidance to author an explicit final SELECT; returned headers cannot invent declarations.

```sql
---
name: prepared_orders
model_type: kater_dataset
custom_sql: true
---
SELECT
  DIMENSION('order_id', o.id) AS "Order ID",
  DIMENSION('amount', o.amount) AS "Amount",
  MEASURE('revenue', SUM(amount)) AS "Revenue",
  MEASURE('average_amount', AVG(amount)) AS "Average amount",
  MEASURE('orders', COUNT(DISTINCT order_id)) AS "Orders"
FROM RAW.PUBLIC.ORDERS AS o
```

Name each warehouse table in full, `database.schema.table`, and read a Kater model by its typed address, such as `kater_dataset.orders`. A save that changes the SQL needs execute on each model it reads and on the synced DB dataset of each table it reads, as raw SQL does. A table with no synced DB dataset, a name without its database and schema, a table function other than `FLATTEN`, a call that is not a built-in function of the warehouse (such as a user-defined function or a macro), and SQL that cannot be parsed are refused, except that an Admin can read a table that is not synced. Whoever may execute the dataset then gets its rows.

The native rowset contains dimensions only. Measures are reusable recipes over this dataset's published dimensions, so `SUM(amount)` refers to its own dimension interface. Aggregating a private raw column not published as a dimension is rejected, as are dimension/measure construction cycles. Every reader reaggregates these accessible rows for SUM, AVG, and COUNT DISTINCT. CLEAR can change removable consuming context but cannot undo the query's own restrictions.

The compiler records native dimension ordinals separately from full analytical output bindings. Pending warehouse types retain the declared names and supported pending state; they cannot send declaration markers to the warehouse. Compile/inspect never executes SQL. A unit with no tabular output publishes no invented fields.

Custom dataset fields belong to the custom dataset and never claim `db_col_name`, even for a direct physical read. Each field's origin is the one its SQL shows: a plain column of a known model or table is that source field, and a calculation or a cast over such columns, or over constants, is computed. Where the SQL shows no origin, such as a column of a table with no known schema, the origin stays unresolved, and the field still compiles from the warehouse's metadata. Labels, source occurrences, recipe dependencies, and definition revisions are preserved in the replacement contract. Each field's own `-- @` directives above its `DIMENSION` or `MEASURE` (description, AI context, business questions, tags, values and format) are published with it, and a model that reads the dataset passes them on with its labels, as it would a standard Kater dataset's. See [query contracts](vfs://guides/query-contract.md) and [measures](vfs://guides/measure.md).

## Dataset keys

A custom SQL dataset may write `-- @primary_key` or `-- @foreign_key` above a final field, with no argument and at most one key on each field. A primary key claims one row per value and no empty value in this dataset. A foreign key permits repeated values. Use a foreign key after a join or an unnest repeats the source's ID; do not claim a primary key merely because the input was unique.

Without an explicit directive, a plain reference, parentheses or a cast keeps the source's key and the dataset that declares it. An explicit key wins and names this custom SQL dataset. A calculation has no inherited key. A set operation or `COALESCE` has a key when every non-NULL value it reads has a key of the same kind; with different source datasets it is a foreign key attributed to the containing model. A key with no format shows as an ID, while `@format id` alone makes no key. See [dataset keys](vfs://guides/dataset__kater.md#dataset-keys).
