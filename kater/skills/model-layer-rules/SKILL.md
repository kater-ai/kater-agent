---
name: model-layer-rules
description: "Model layer rules. Use it when the person asks for: Data definitions that everyone shares: measures, dimensions and joins; A shared dataset that shapes warehouse tables, or custom SQL."
---

This skill is Kater's guide `vfs://guides/model-layer-rules.md`.

# Model layer rules

`model_rules.json` in `packages/model-contracts`, policy schema version **1**, owns model creation, direct source eligibility, source occurrence limits, metadata, relationships, joins, parameter/filter capabilities, and SQL statement dispatch. Its `layers` are the layer types of [Key concepts](vfs://guides/concepts.md). `authoring_contexts.query` is a browsing/creation context inside Workspace, never another persisted layer. The Python and TypeScript adapters interpret this file; consumers must not copy the matrix into independent conditionals.

What each model reads, and how many, is the diagram of how objects connect in [Key concepts](vfs://guides/concepts.md), and what each may use is its Model types table; both are generated from `model_rules.json`. A custom SQL dataset's reads are in [custom SQL datasets](vfs://guides/dataset_custom_sql__kater.md).

The policy governs compiler validation, source discovery, creation, and runtime consumers. Ordinary Kater datasets permit linear local wrappers around one expanded physical occurrence. The compiler validates resolved lexical occurrences in root and imported definitions before lowering: joins/self-joins, repeated references, hidden or unused extra inputs, sets, recursive CTEs, and forbidden sources fail at authored locations. An imported dataset’s `custom_sql` flag must match its declaring frontmatter. Generated compiler CTEs do not contribute to the authored count. See [standard datasets](vfs://guides/dataset__kater.md) and [physical DDL](vfs://guides/dataset__db.md). Custom SQL preserves full native SQL and publishes explicit output state; see [custom SQL](vfs://guides/dataset_custom_sql__kater.md). Charts retain their existing constrained projection grammar and schema.

A relation's key is the one that [Key concepts](vfs://guides/concepts.md) describes, and no key holds a file path; `docs/object-identity.md` has the parts of each kind's key. A `local` key is an internal lexical identity: its owning top-level key, the scopes it sits in and its name; `local` is never authored frontmatter.

Source browsing lists physical declarations. Shared browsing lists Kater datasets and reusable semantics. A browsing list is not permission to select a source: pickers require an explicit active consumer type, namespace, and custom SQL variant. Missing active context cannot grant source eligibility. Editing exclusion compares complete identities, including kind and name; hiding a query also hides charts whose declared parent is that exact query. Source retains its own read-only catalog entry. Chart pickers additionally require the exact declared parent key. Merely choosing a path named `.kater/sandbox` grants no exception; The service supplies a server-created ephemeral workspace rooted in one shared semantic or Kater dataset for anonymous Explore.

The Python adapter is `kater_model_contracts.model_rules`; the editor adapter is `packages/kater-ui/src/workspace-panel/protocol/model-rules.ts`. Both run the common acceptance fixture at `tests/fixtures/model-policy.json`. Creation menus use `authoring_contexts`; compiler and insertion permissions use `layers.<layer>.models.<model_type>`, selecting the `custom_sql` variant only when explicitly true.

A shared model is named by its typed address, `<kind>.<name>`, such as `semantic.orders`, `kater_dataset.orders` or `db_dataset.orders`, as `kater model show` and a test's `model:` take it. A semantic model's file is `vfs://connections/<connection>/modeling/kater_objects/semantic_models/<name>.sql`, and a Kater dataset's is `vfs://connections/<connection>/modeling/kater_objects/datasets/<name>.sql`. `kater model list` gives each model's id (`rel1_…`), which a move of its file keeps.

## The parts

- **Explore and the Catalog, the Sandbox, custom calculations and promotion:** [Explore, the Sandbox, custom calculations and promotion](vfs://guides/model-layer-rules-promotion.md).
- **Named final fields, metadata and addresses, replacement and caches, shared definitions, a workspace's lifecycle, the active authoring scope and key directives:** [fields, addresses and caches](vfs://guides/model-layer-rules-fields.md).
