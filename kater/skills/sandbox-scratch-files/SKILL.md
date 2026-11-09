---
name: sandbox-scratch-files
description: "Sandbox: scratch files and statements. Use it when the person asks for: A field you are unsure of, to try before a query uses it."
---

This skill is Kater's guide `vfs://guides/sandbox-scratch-files.md`.

# Sandbox: scratch files and statements

A part of [Sandbox](vfs://guides/sandbox.md).

## Scratch files

A scratch file is yours: any `.sql` file in your brainstorm folder, `connections/<connection>/users/<your email>/brainstorm/`, at any depth, except in a topic's `workspaces/` and `apps/` folders. See [the brainstorm folder](vfs://guides/brainstorm.md) for the rest of what it holds.

- **Where.** A loose file goes at the top, `brainstorm/<name>.sql`. A file for one question goes in that topic's folder, beside its plan, such as `brainstorm/promote_roas/checks.sql`. Any folder works, `brainstorm/sandbox/` included.
- **Known by its path.** A scratch file's identity is its path in the brainstorm folder, so two topics can each hold a `checks.sql`. `name` in its frontmatter is still its file's name.
- **Folders that hold no scratch files.** A topic's `workspaces/` folder holds workspace copies, whose `.sql` files are their models ([brainstorm](vfs://guides/brainstorm.md), Copies). Its `apps/` folder holds draft apps ([brainstorm](vfs://guides/brainstorm.md), Draft apps), which hold no SQL, so a `.sql` file there is refused, and so is one in `users/<your email>/sandbox/`, where scratch files were before 2026-10-01.
- **Never versioned.** The brainstorm folder is gitignored, so a scratch file never reaches a branch or anyone else's checkout. A scratch file made in a local copy also stays out of the portal ([brainstorm](vfs://guides/brainstorm.md)). Like the rest of your Personal folder, only you, admins, and whoever you share a file or a folder of it with can read it. Share… in Files shares one, and it's then under their Shared with me.
- **Never expires.** Delete one with `delete_file`, or several files and folders at once with `kater rm PATH…`; a folder goes with everything in it, and each delete is in the Trash for 30 days, where `kater restore ID` brings it back. Rename or move one with `kater files move FROM TO`, which gives a renamed scratch file's frontmatter its new `name`. A parameter or a filter that the file declares in its own `parameters:` renames with `kater rename FILE#NAME NEW_NAME`, which shows the preview, and the same line with `--write` through `kater_apply`: the declaration, each `:name` and the filter's name change together, since the file's filter and its parameter share one name.
- **Nothing reads it.** No model, picker, completion, Catalog page or Explore card can select from a scratch file.
- **Saving never fails on a statement.** `write_file` and `edit_file` save a scratch file even when a statement doesn't compile, and never format it. Only the frontmatter is checked, and `name` must be the file's name. A run is what reports a statement's errors.
- **Creating one.** `kater new scratch`, with `--folder <topic>` to put it in a topic's folder of your brainstorm folder. Left without a `name`, it's named `unnamed_scratch_<n>`, numbered across the whole brainstorm folder, with no label. A name you give is refused when its folder has one. `kater scratch append --request-file -` without a `path` also makes one, with its first statement. Add each statement as [Writing a statement](#writing-a-statement) says.
- **Showing one.** `kater brainstorm show FILE` lists a scratch file's statements with what each reads, and runs nothing. In a chat that draws apps it draws each statement with Run, which the person runs there: a statement that reads one model or one workspace draws as its chart, in the chart `charts:` keeps, and any other as its rows. `kater run --select FILE` gives you the rows to read, and in a chat that draws apps it draws each statement's result as Kater's chart, with its link to Kater, so the person sees every figure you quote where it came from. Never draw those rows again with a chart, table or artifact of your own: it links to no query and keeps no definition.
- **Finding yours.** `kater scratch files --request-file -` with the `connection` lists your whole brainstorm folder: each topic with its note, its folder, and the title and `Status:` its note opens with; every folder; and every file, newest first, with its `kind` (`scratch`, `note`, `page`, `model` for a Kater object in a workspace copy, or `other`) and when it was last saved. Among them, by date, are the scratch files someone shared with you from their own brainstorm folder (an admin's list holds everyone's); each file's `ownership` is `mine`, `shared` or `others`, and `owner` is whose folder it's in. `kater scratch list` lists the scratch files, naming whose each one is when it isn't yours. `read_file`, or `source.read` through `kater invoke`, reads any text file there. `kater scratch append --request-file -` adds one titled statement as a new scratch file at the top, labelled with its title, or, with `path`, after the last statement of one of yours.
- **Open in Sandbox.** Explore cards, a Catalog page's saved cards, a shared model's Definitions examples, every chart's Add to → Open in Sandbox and a shared model's File, Open in Sandbox (its first 20 rows, `SELECT * FROM` the model, in the shared model editor in the portal, Files and VS Code) each offer it. It opens the item's query as a new scratch file, titled with the card's title or the example's, and opens the Sandbox on it in a tab of its own without running it; several cards opened together go into one file. An Explore card's query reads its model by its shared name, with each condition kept as its optional filter over a parameter the file declares at the card's value: `explore.card.sandbox` (`kater explore card open`). A saved query or chart (Add to → Open in Sandbox, a dashboard tile's Open in Sandbox) opens as your draft of the query, `draft_<query name>.sql`: the one you have, as you left it, or a new one with its SQL as written, the parameters its frontmatter declares, a chart's chart, and `from:`, which names the query and places every statement of the file in its workspace, so `query.<name>` reads that workspace's queries with no read of its root, and its runs take the filters the chart was drawn with: `query.sandbox`, or `files.add_to` with `to: {kind: sandbox}` (`kater add-to PATH --sandbox`). You have one draft of each query.

A file is one frontmatter block, then any number of statements separated by semicolons, as in a Snowflake worksheet. This is the file the three calls above make, with a second statement of warehouse SQL:

```sql
---
name: checks
model_type: scratch
label: Ad spend profiling
---

-- July 2026 spend by platform
SELECT
  ad_reporting.platform,
  ad_reporting.union__currency,
  ad_reporting.sum__spend
FROM semantic.ad_reporting AS ad_reporting
GROUP BY
  ad_reporting.platform,
  ad_reporting.union__currency
ORDER BY
  3 DESC NULLS LAST;

-- dbt ad report mart, July 2026
SELECT platform, SUM(spend) AS spend
FROM DEMO_DB.ANALYTICS_AD_REPORTING.AD_REPORTING__AD_REPORT
WHERE date_day BETWEEN '2026-07-01' AND '2026-07-31'
GROUP BY platform;
```

The first statement also holds its rows to July 2026, from its `is_between` pick. The writer writes that condition in its `WHERE`, with its value in its call, as [Conditions](#conditions) says. This example leaves it out. Never write that condition yourself.

| Key | Required | Meaning |
| --- | --- | --- |
| `name` | yes | The file's name, without `.sql`. |
| `model_type` | yes | `scratch` |
| `label` | no | The name people see. |
| `description` | no | What the file is for. |
| `parameters` | no | The parameters its statements' conditions read; see [Conditions](#conditions). |
| `charts` | no | Each charted statement's chart; see [Charts](vfs://guides/sandbox-charts.md#charts). |

## Writing a statement

A statement is made the way a query is: from picks, not typed by hand. Read `connections/<connection>/ai_tools/skills/query_authoring.md` first and follow it, with two changes: the picks go to `sandbox.statement.write` instead of `query.new`, and its answer goes into the file with `sandbox.append`.

1. **List the fields.** `kater query fields WORKSPACE`, with the workspace the statement reads: one of yours, a copy in your brainstorm folder, or one over the shared model it reads. It lists each field with its menu of operations. `kater scratch catalog --request-file -` with `{"connection": "<connection>"}` lists every shared model's fields, without menus. It takes only `connection` and has no search: to find a field by a word, such as `campaign`, use `search_catalog`.
2. **Write the picks.** Send them to `kater scratch statement.write --request-file -` inside `picks`, with the names `query.new` uses (`fields`, `conditions`, `group`, `order`, `limit`). Beside `picks`, send the scratch file's `namespace` and `path`, and what the statement reads: a `workspace` by its folder, or a shared model as a typed `source`.

   ```json
   {"namespace": {"kind": "scratch", "connection": "<connection>", "user": "<your email>"},
    "path": "connections/<connection>/users/<your email>/brainstorm/<topic>/checks.sql",
    "workspace": "connections/<connection>/users/<your email>/workspaces/<workspace>",
    "picks": {"fields": [{"field": "platform"}, {"field": "sum__spend"}],
              "order": [{"field": "sum__spend", "descending": true}]}}
   ```

   It saves nothing. Its `sql` is the statement. For a shared model, send `"source": {"name": "ad_reporting", "model_type": "semantic", "namespace": {"kind": "shared", "connection": "<connection>"}}` in place of `workspace`, as [A statement in three calls](vfs://guides/sandbox.md#a-statement-in-three-calls) does. To change a statement the file has, send its `statement` number in place of both. When Kater answers that it is not ready (`not_ready`), the request is right: wait a minute and send it again, unchanged.
3. **Put it in the file.** `kater scratch append --request-file -` with the `connection`, the answer's `sql` and `parameters`, a `title` and the file's `path`. The file's frontmatter then declares each parameter the answer names. A pick's condition needs none. A `path` that isn't saved yet is made, and without `path` the statement opens as a new scratch file. A changed statement replaces the old one in place, with `edit_file`, and its new `parameters` go in the frontmatter.
4. **Write SQL only when no pick makes it,** such as a join or warehouse SQL, and say which pick you tried. Then follow the skill's rules for Kater SQL: a `KATER__` function where one exists, a field selected bare, and no CTEs or subqueries.
5. **Run it** with `kater run --select FILE`, and read the rows before you show them.

## Statements

- **Numbered from 0,** in file order, everywhere: `kater show --select FILE --statement N`, `kater compare`'s `FILE#N` and `charts:`.
- **A title** is the run of `--` comment lines directly above a statement's first token, joined with spaces. A comment at the end of the previous statement's line isn't one. Titles name results, and a query added to a workspace, so give every statement one.
- **A position** is a zero-based line and UTF-16 character, as `at` takes it. It belongs to the statement it falls in; one just after a `;` belongs to the next statement.

## Conditions

In picks, a condition names its `field`, its `operation` and its `parameters`. `is_equal_to` and the other comparisons take `value`. `is_between` takes `range`, a list of two values: `{"field": "order_date", "operation": "is_between", "parameters": {"range": ["2026-01-05", "2026-03-29"]}}`. `is_one_of` and `is_not_one_of` take `values`, a list. `text_contains` and `text_does_not_contain` take `text`. `is_null` and `is_not_null` take none. `is_within_last` takes `amount` and `unit`.

A statement over one model holds its rows to conditions the way a query does: each is an optional filter with its value in its call, and the frontmatter declares no parameter for it. The Sandbox's chart writes them this way. A condition that Open in Sandbox or an Explore card brings stays over a parameter that the frontmatter declares.

The writer writes each one in the statement's `WHERE`, with its value, its label and `on=TRUE`. Never write the condition yourself: change the pick and write the statement again. A date condition takes a relative date, such as `is_within_last` with `amount: 6` and `unit: month`, so the statement moves with each day.

Take each value of `is_one_of`, `is_equal_to` and the like from the field's real values: `kater field values PLACE FIELD` lists them, with `--search` for the values that hold a text. In a saved query or a workspace, a filter is changed with `kater filter`, never in the SQL ([filters](vfs://guides/filters.md)).

- **A run takes the reader's choice kept for the draft, else the default in the call.** A pick's filter writes `on=TRUE`, so the statement runs as it reads. To change a value, change the pick and write the statement again: a statement's run takes no inputs.
- **Its chart can switch one off.** That's `sandbox.chart.preview`'s `inputs`, whose `filters` names the ones on and `off` the ones off; the statement keeps the condition as written. A switch is the reader's, never the file's: Kater keeps it for the reader and the draft (`dashboard.inputs.save` with `consumer: {"kind": "draft", "path": "<the draft>"}` and `query: "draft"`), and `charts:` keeps none.
- **Statements share a declared parameter** that is declared the same way. When a chart writes one at another value, it takes the next free name, `…_2`, and a parameter nothing reads any more goes.
- **Only the `query` and `workspace` routes read parameters.** A statement over several models, or warehouse SQL, writes its conditions in at their values. A statement that reads a workspace also reads its root's parameters, each at its default, as a query saved there does.
