---
name: semantic-model
description: "Shared semantic models. Use it when the person asks for: Data definitions that everyone shares: measures, dimensions and joins."
---

This skill is Kater's guide `vfs://guides/semantic-model.md`.

# Shared semantic models

In a person's own terminal, `kater new semantic-model` writes the file. Through the Kater MCP, write the whole file with `kater source write connections/<connection>/modeling/kater_objects/semantic_models/NAME.sql --text-file -` through `kater_apply`, after the person says yes.

Read `kater model show semantic.NAME` for the model's card: its source models or SQL, relationships, required filters and fields, in the order the model works them out. Each part opens its own card in the portal. `kater model explain FIELD --relation semantic.NAME` reads a field's definition and its breadcrumbs, including the steps a source made. Change a source's step in its own layer; the model's SQL is the only record of how its fields are made.

Shared semantics compose the sources permitted by the [central model policy](vfs://guides/model-layer-rules.md). They cannot introduce workspace parameters or optional filters. Required literal restrictions and named relationships remain attached to their defining stage.

A field that a semantic model or a Kater dataset declares is Verified only after a person who can change the model reviews its definition: in the model's page, or with `kater review <model>.<field>`. Kater keeps the review as a `-- @reviewed` line above the field, and a change to the field's definition takes it away. A Kater dataset's field that only shows a column, as each `DIMENSION('id', orders.id)` that sync writes does, needs no review. A new field, and a field whose definition you change, waits for a review: tell the person which fields wait, and say that they are not verified. Never run `kater review` on your own, and never write a `-- @reviewed` line.

Add fields through `edit.model`, as [Public creation workflow](vfs://guides/public-creation-workflow.md) says. A source's field is added as its bare name, like `s.revenue` below, and a field an operation computes is declared over its `KATER__` call. A field's classification, format and non-additive rule, and a relationship to another model, are operations too: `field.classify`, `format.display`, `field.not_additive_across`, and `edit.model`'s `relationships` ([relationships](vfs://guides/relationship.md)). Write by hand what no operation expresses, such as `net_revenue` below: `create_projection` isn't offered in a shared model.

```sql
---
name: sales
model_type: semantic
---
SELECT
  s.region,
  DIMENSION('territory', s.region) AS "Sales territory",
  s.revenue AS "Revenue",
  MEASURE('net_revenue', EVAL(s.revenue) - EVAL(s.refunds)) AS "Net revenue"
FROM kater_dataset.sales AS s
```

The first projection evaluates and preserves the dataset dimension's key. The second intentionally creates a semantic-owned dimension. The third passes the dataset measure through: it keeps the dataset measure's key and adds this model's context, such as its WHERE. A star does the same for every measure. The fourth creates a semantic-owned reusable recipe with the dataset measures as dependencies. AS labels do not alter any of these identities. Declare a new measure only when the definition changes, and give it a new name: a `MEASURE` or `DIMENSION` with the name of a field of a model it reads shadows that field, with the warning `field_name_in_scope`.

Names come from the declaration literal. Complete namespace, model kind/name, field kind, and identifier distinguish declarations; dependency occurrences distinguish repeated inputs and self-joins. A downstream model selects public names, never labels, hashes, or private CTE aliases. An unwrapped expression that isn't a pass-through stays unnamed until it's declared, so no other model can read it; its type is its formula's, and a semantic model publishes no custom calculations. A window here is a dimension ([windows and custom calculations](vfs://guides/window.md#a-window-in-a-shared-model)).

Custom dataset measures aggregate their published dimension rows, and every reader runs the dataset's query. Definitions preserve complete formulas and context programs. [Measures](vfs://guides/measure.md) documents supported CTE reuse and exclusions. [Query contracts](vfs://guides/query-contract.md) explains local/exported roles, exact bindings, and runtime snapshots.

Rename a semantic model with `kater rename semantic.<name> NEW_NAME`, then the same line with `--write` through `kater_apply`, never by editing its `name`. One change renames every place that names it, in every folder: each model and query that reads it, its tests files and its Explore definition, which move to the new name, and its file when the file has the model's name. A workspace that wraps it keeps its name. A name that another model or dataset of the shared layer has stops the rename. So does a name that a rename took from another model or dataset of the connection: it stays with that model, and only a rename back gives a model its own old name again. The old name still resolves in a file the rename could not reach, and History's Undo takes the whole change back.
