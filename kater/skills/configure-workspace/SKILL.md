---
name: configure-workspace
description: "Add the fields, filters and parameters an analysis needs to a workspace's root. A step of deep-analysis."
---

This skill is Kater's guide `vfs://guides/configure-workspace.md`.

# Configure a workspace

Add the minimum reusable selected-workspace root interface required by a built analysis DAG, then prove its compilation.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## Procedure

Read the workspace's parts with `kater model show workspace.NAME`. A field's definition card includes its breadcrumbs from the SQL, including the steps its source made. Change a source's step in the source's layer. A field whose value is hand-written SQL keeps that SQL as its start; do not invent a separate list of steps for it.

1. Confirm the selected workspace, as the run recorded it, is a writable personal workspace. Read its authored root `workspace.sql`, generated semantic contract state, and every semantic object-status row for that workspace. A workspace this run didn't create already answers someone's questions: change its root in a copy first (`kater brainstorm copy WORKSPACE --topic TOPIC`, vfs://guides/brainstorm.md), prove the copy's queries keep their numbers with `kater compare --left WORKSPACE --right COPY`, and after the user's yes write the copy back with `kater workspace apply COPY` (dry run first), then compare again.
2. Reuse the one typed shared source selected by the workspace declaration. A root without one gets it from `workspace.source` (`kater workspace source use MODEL --scope WORKSPACE`): a readable same-connection semantic or Kater dataset, including custom SQL. A dataset requires no semantic intermediate. The `SELECT *` it writes passes the source's measures through.
3. Add only the missing fields, metadata, parameters, or filters that query contracts in the DAG need, through the typed operations in vfs://guides/public-creation-workflow.md: `edit.model` with `add` for a source field, a field's offered `aggregations`, `derive`, `comparisons`, `windows` or `calculate_at` operation (calendar comparisons belong in a receiving query) for a computed one, its `format.display`, `field.classify` and `field.not_additive_across` operations for its format, classification and non-additive rule, `kater filter add QUERY FIELD --workspace` (QUERY as `<workspace>/<name>`) with a condition flag for a filter, with its values from `kater field values`, `kater filter set` for a filter's label, description, AI context and switch, and `kater parameter add`, `save` and `remove` for a parameter (vfs://guides/parameters.md). Never write a filter call or a parameter's entry by hand. A star or bare name passes upstream measures through with their keys and the root's filters. Keep the root's `*`, and never write its fields out by hand. Every operation keeps it: a field you add goes after the `*`, and a field you change or take out leaves the `*` through `* EXCLUDE (…)`, a changed one on its own line. The `*` still passes through what its source adds later. Only moving a field writes every field out. Declare a new measure only when the definition changes, writing it by hand with `update_workspace_model` or `kater source write`. Keep run-only values, including sensitivity and thresholds, out of reusable frontmatter.
4. Each operation and each hand-written save compiles `workspace.sql` under central model policy, and refuses a change that adds an error. Only workspace roots and queries may own Kater parameters and OPTIONAL_FILTER predicates; REQUIRED_FILTER has no Kater parameters. Queries read the root's parameters without redeclaring them. An optional filter's default is `on=TRUE` in its own call; a chart and the dashboard keep no filters, and each reader's own switch is kept for them on their page. A filter moves up only, from a query to the root, with `kater filter move QUERY NAME`, which takes away the other queries' copies of its condition; nothing moves one down. Read derived interfaces through the shared compiler catalog.
5. Compile the explicit workspace. Record compiler diagnostics, generated identities, and a bounded sample-run result in the analysis note; change the associated status rows to `built` only after both validation and compilation pass.

## Stop conditions

Stop as `blocked` when the selected source or a required field is missing, the workspace is not writable, or compiler validation fails. Preserve the exact missing object or diagnostic; do not bypass the compiler with raw SQL or generated-file edits.

## Canonical public creation

Read the current `vfs://guides/kater-cli-commands-masterlist.md` through the Kater MCP (a local copy has no guides) and CLI help. Create through `kater new workspace PARENT/NAME --from-model KIND.NAME`, with PARENT the folder of workspaces it goes in, `kater workspace source use MODEL --scope WORKSPACE` for a workspace created without a source, `kater new query WORKSPACE/NAME --query-file QUERY.sql`, and `kater new chart WORKSPACE/NAME --source-query QUERY --definition-file PROPOSAL.json`, supplying `--kater-root` and the exact complete workspace scope. These commands share public scaffold options/draft/create, revision witnesses and editor-aware application. A query document includes frontmatter and exactly one named query; chart proposals use the current `chart.plan` request schema. Preserve returned identities and buffer/save outcomes; do not retry creation after a navigation failure. App scaffolding remains governed by its separate build-app-ui contract.

For calendar comparisons, read vfs://guides/period-comparisons.md. Add the comparison in the receiving query through its offered `comparisons` operation. Keep the exact workspace path, bound date axis and output reference; do not plan from a label or manually pair a grain and a unit. Query-local comparisons are Custom Calculations; local conversion to a reusable measure is available only when the compiler offers it. Shared-model promotion is deferred and is not a named operation.
