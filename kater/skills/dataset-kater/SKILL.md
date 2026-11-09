---
name: dataset-kater
description: "Standard Kater dataset. Use it when the person asks for: A shared dataset that shapes warehouse tables, or custom SQL."
---

This skill is Kater's guide `vfs://guides/dataset__kater.md`.

# Standard Kater dataset

Read `kater model show kater_dataset.NAME` for the dataset's card: its source database model, required filters and fields. A field's card shows its Expression and breadcrumbs, derived from its SQL. Read the database model's card for the warehouse table and columns; each part's chip opens that part's own card in the portal.

A standard dataset enriches one physical table under the central [model policy](vfs://guides/model-layer-rules.md). `kater sync --resource-type kater_dataset` writes it from the table's DDL and keeps what you add after the columns. Through the Kater MCP, `kater sync` runs only with `--dry-run`, so ask a person to run it. Every physical column must appear first, unchanged, in a `DIMENSION('physical_name', source.column)` declaration. The literal must keep the physical identifier's identity; AS is only a display label. An unquoted name is written in lower case, and a quoted one exactly, with SQL identifier/literal escaping, never slugging.

```sql
---
name: orders
model_type: kater_dataset
---
SELECT
  DIMENSION('id', orders.id) AS "Order ID",
  DIMENSION('amount', orders.amount) AS "Amount",
  DIMENSION('tax', orders.tax) AS "Tax",
  DIMENSION('adjusted_amount', orders.amount + orders.tax) AS "Adjusted amount",
  MEASURE('sum__amount', KATER__SUM(orders.amount)) AS "Revenue"
FROM RAW.PUBLIC.ORDERS AS orders
```

This example assumes ID, AMOUNT, and TAX are the complete physical schema. Sync wrote the first three declarations, the `aggregations.sum` operation wrote `sum__amount`, and `adjusted_amount` is written by hand, since no operation adds two fields. Missing, renamed, transformed, or reordered-after-additions bases fail correspondence validation. Add explicitly named calculations after the complete baseline instead of replacing it. Dataset fields have dataset-owned keys; only unchanged bases carry `db_col_name`. Measures and calculated dimensions retain named dependencies without claiming direct physical correspondence.

A field's `field.classify` operation writes its classification directive, `format.display` its format, `format.style` and `format.highlight.add` how a table draws its cells (`-- @style` and `-- @highlight`, see [Dimension](vfs://guides/dimension.md)), and `field.not_additive_across` a measure's non-additive rule. Description, AI-context, business-question and tag directives are written by hand. A field's label is its AS. Dataset sync adds or renames bases from physical metadata while preserving authored additions and labels. It reports naming collisions and dependent reference changes; inspect the proposed transaction before applying it.

Downstream, a bare selection passes both field kinds through with their keys, and `EVAL(orders.sum__amount)` publishes a measure's value. A new measure or named value built on it needs a name no field in its scope has, such as `DIMENSION('amount_value', EVAL(orders.sum__amount))`. See [measures](vfs://guides/measure.md), [physical datasets](vfs://guides/dataset__db.md), and [custom SQL datasets](vfs://guides/dataset_custom_sql__kater.md).

Rename a Kater dataset as a semantic model is renamed, with `kater rename kater_dataset.<name> NEW_NAME` ([semantic models](vfs://guides/semantic-model.md)). Sync's record of the dataset follows the new name, so the next sync keeps the dataset and the fields you added. A DB dataset renames with `kater rename db_dataset.<name> NEW_NAME`, and its file stays where it is ([physical datasets](vfs://guides/dataset__db.md)).

## Dataset keys

Only a DB dataset or a Kater dataset, including custom SQL, may write `-- @primary_key` or `-- @foreign_key` above a field. Each takes no argument, and a field takes at most one. A primary key means one row for each value and no empty value. A foreign key means the value is another table's key and can repeat here; its join is still a `RELATIONSHIP`, which names the target and the join condition. `@unique` is no longer a directive.

A standard dataset restates the keys it reads from its one table as its own. Its published field has `key: {kind: primary|foreign, dataset: kater_dataset.NAME}`. A field passed downstream by plain reference, parentheses or a cast keeps that key. A calculation such as `LEFT(order_id, 3)` has none. Set operations and `COALESCE` keep a key only when every non-NULL branch or argument reads a key of the same kind; different source datasets then give a foreign key attributed to the containing model. A key with no format of its own shows as an ID. `@format id` only controls display and never makes a key.

Write a key on the dataset where the field comes from, never on a semantic model, workspace or query. For a synced table, ask a person to accept a suggested foreign key in their own terminal, with `kater sync --resource-type db_dataset --foreign-key orders.customer_id`; do not edit the generated DDL. See [physical datasets](vfs://guides/dataset__db.md) and [key filters](vfs://guides/filters.md#keys-filter-as-ids).

`kater model show REF` gives the object's composed card. To inspect each field's authoritative key, use `kater model inspect MODEL_FILE --feature contract --json`: `inspection.contract.output.fields` carries each field's `key` with its `kind` and `dataset`. A field with no key omits that property.
