---
name: tests-writing
description: "Tests of shared models: writing a test. Use it when the person asks for: A check that a shared model still gives the numbers it should."
---

This skill is Kater's guide `vfs://guides/tests-writing.md`.

# Tests of shared models: writing a test

A part of [Tests of shared models](vfs://guides/tests.md).

## The file

Each test names the model it tests in `model:`, as `<kind>.<name>`, such as `model: semantic.ad_reporting`. It is required, and every test of one file names the same model. The file's name is only a label: a model's tests are found by their `model:`, whatever the file is called. A model's first test goes in a file named after the model, `tests/ad_reporting.sql` for `semantic.ad_reporting`, and each test after it goes in the file that already names the model. When the model is renamed, each test's `model:` changes in the same change, and a test file named after the model moves to the new name. When a field is renamed, each test that reads it changes in the same change: its SQL, its picker, and its `expect.columns` and `tolerance` where the test outputs the field by its own name. A name the picker made from the field, such as `across_channel__orders`, keeps its name. There are two places for it:

| Where | Who reads it | Who writes it |
| --- | --- | --- |
| `connections/<connection>/modeling/kater_objects/tests/`, the shared layer's | Everyone who reads the model. | Whoever edits the shared models. Its tests are the model's own: readiness, the nightly run and the model's tests chip count only these. |
| `connections/<connection>/users/<email>/tests/`, in your own folder (`connection.folder` of `kater_start`) | You, whoever you share it with, and admins. | You. |

**A new test goes in your own file** unless you ask for the shared layer's: `--shared` on `kater new test` and `kater test generate`, "The shared layer" in Save as test, and `shared: true` on `test.create` and `test.generate`. Only someone who edits the shared models can choose it. Every person's folder starts with an empty `tests/` folder, and a file appears with its model's first test. Your own tests name their model in `model:` just as the shared ones do, so the two files hold tests of the same model and each keeps its own names. To hand a test to the data team, they copy its block into the shared file.

Each test is one block with its own frontmatter, separated the way a workspace's query file separates queries. Test names are unique within the file.

```sql
---
name: july_2026_totals_by_platform
model_type: test
model: semantic.ad_reporting
description: Matches the Ads Manager exports for July 2026.
expect:
  columns: [platform, union__currency, sum__spend, sum__clicks]
  rows:
  - [facebook_ads, USD, 63963.91, 58844]
  - [google_ads, USD, 110747.05, 79509]
  - [tiktok_ads, USD, 12535.44, 19704]
---

SELECT
  ad_reporting.platform,
  ad_reporting.union__currency,
  ad_reporting.sum__spend,
  ad_reporting.sum__clicks
FROM semantic.ad_reporting AS ad_reporting
WHERE
  KATER__IS_BETWEEN(ad_reporting.union__date_day, CAST('2026-07-01' AS DATE), CAST('2026-07-31' AS DATE))
GROUP BY
  ad_reporting.platform,
  ad_reporting.union__currency
```

A test is `model_type: test`. Nothing reads it: it isn't in pickers, completions, the Catalog or Explore. It compiles through the sandbox (`compile_through: sandbox` in the [model-layer rules](vfs://guides/model-layer-rules-promotion.md)): each path compiles as a query in the in-memory workspace Explore builds, rooted in the model and named after it. SQL that reads `semantic.ad_reporting` reads it through that root, so a test behaves exactly as a stakeholder's query does: grouping, `KATER__` conditions and `NOT_ADDITIVE_ACROSS` apply.

## Keys

| Key | Required | Meaning |
| --- | --- | --- |
| `name` | yes | Unique within the file. |
| `model_type` | yes | `test` |
| `description` | no | What you checked the answer against. It is never written for you. |
| `tags` | no | Model tags, for selecting tests with `--select tag:KEY=VALUE`. |
| `model` | yes | The model the test tests, `<kind>.<name>`: `semantic.NAME` or `kater_dataset.NAME`. Every test of one file names the same model. A connection workspace's tests name none: they read its root. |
| `question` | no | The model's business question the test answers, word for word. `kater test generate --from-questions` writes it for each question, and `--question TEXT` for one. |
| `draft` | no | `true` until you've checked the answer. Drafts run and report. A draft can leave out `expect` while you write it: its first run that returns rows records them. |
| `as_of` | no | The day the test takes as today, such as `'2026-09-27'`, or a date and time. It pins relative dates. |
| `inputs` | no | Only in a connection workspace: `parameters` and `filters`, what the root runs with. |
| `given` | no | A fixture: each table the model reads, with the rows it holds instead. Every other table it reads is empty. |
| `picker` | a picker, SQL, or both | A `query.new` request without `namespace`, `name` or `dry_run`: `fields`, `conditions`, `group`, `order`, `limit`. |
| SQL body | a picker, SQL, or both | Hand-written Kater SQL that reads the model. |
| `expect.columns` + `expect.rows` | one expectation | The exact answer. |
| `expect.same_as` | one expectation | Another query whose answer must match, and the `key` columns that pair its rows. `sql` is Kater SQL over one shared model; `native` is read-only warehouse SQL, such as a query of a dbt mart. |
| `expect.refused` | one expectation | The diagnostic code the compile must fail with, such as `not_additive_across`. |
| `expect.tolerance` | no | Absolute tolerance by column name, where the format's precision is wrong. |

## Two paths, three expectations

Each path a block has must produce the expected answer, and each is checked on its own, so a failure names the path that broke.

- **A picker** tests what a stakeholder's clicks produce. The block stores the request, not the SQL: the SQL writer writes it at every run, so the test follows the writer. Set `group: true`, so a query returns one row per combination rather than one per underlying row.
- **A SQL body** tests hand-written Kater SQL.
- **Both** test that the two paths agree.

| Expectation | Passes when | Warehouse |
| --- | --- | --- |
| `rows` | Every path returns exactly the rows, as below. | Each path runs. |
| `same_as` | Every path's answer equals the other query's. | One diff query per path. |
| `refused` | The SQL fails to compile with that code. A refused test has only SQL: the picker never writes a refused query, it adds what the query needs instead. | Never. |

```yaml
expect:
  same_as:
    key: [platform, union__account_id, union__date_day]
    native: |
      SELECT
        platform,
        CAST(account_id AS VARCHAR) AS union__account_id,
        DATE_TRUNC('MONTH', date_day) AS union__date_day,
        SUM(spend) AS sum__spend
      FROM DEMO_DB.ANALYTICS_AD_REPORTING.AD_REPORTING__AD_REPORT
      WHERE date_day BETWEEN '2025-05-01' AND '2026-07-31'
      GROUP BY 1, 2, 3
```

## Every test pins a fixed row set

Each side of a test that runs filters the model's date field to the same closed range, so the answer doesn't move as data loads:

- a picker condition, `operation: is_between` with `range: ['2026-07-01', '2026-07-31']`
- a WHERE in Kater SQL: `BETWEEN` two dates, or `KATER__IS_BETWEEN`
- a WHERE in native SQL: `BETWEEN` two dates, or a lower and an upper bound on one column
- a relative window, pinned by `as_of`: `is_within_last` with `complete: true`, or bounds that count from `CURRENT_DATE`
- in a connection workspace, a root filter the test's `inputs` turn on over a closed range

A test without one, or whose sides read different ranges, is refused when it's saved and when it runs. A range that ends within 30 days of today gets a warning, because ad platforms restate recent conversions. A refused test only compiles, so it needs no range. A fixture's tables hold only the rows it gives, so its answer can't move, and it needs none either.

## Relative dates: `as_of`

A relative condition, such as "the last 2 complete weeks", moves every day. `as_of` pins the day the test takes as today, so the window is fixed:

```yaml
name: spend_last_two_complete_weeks_by_platform
model_type: test
model: semantic.ad_reporting
as_of: '2026-08-03'
picker:
  fields:
  - field: platform
  - field: union__currency
  - field: sum__spend
  conditions:
  - field: union__date_day
    operation: is_within_last
    parameters: {amount: 2, unit: week, complete: true}
  group: true
```

- **Every reading of today is pinned.** Each `CURRENT_DATE`, `CURRENT_TIMESTAMP` and `GETDATE()` in the SQL the test runs becomes `as_of`, in the test's own SQL and in any model it reads. A relative parameter value in a workspace's `inputs` resolves against it too.
- **A date is its midnight,** on the warehouse's own clock: `as_of: '2026-08-03'` is 2026-08-03 00:00. Give a time, `'2026-08-03T09:00:00'`, for a window counted in hours.
- **The window must end.** `is_within_last` without `complete: true` has no end, so rows after today would enter it: the test is refused. A window ending at the latest reported date (`end: latest`) moves as data loads, and is refused too.

## Fixture tests: `given`

A fixture test runs the model on rows you give, like a dbt unit test. It pins behaviour no real window shows, such as a case the data doesn't have yet:

```yaml
name: account_with_two_currencies_counts_spend_twice
model_type: test
model: semantic.ad_reporting
given:
- table: facebook_ads__account_report
  rows:
  - {SOURCE_RELATION: fb, DATE_DAY: '2026-07-01', ACCOUNT_ID: 1, CURRENCY: USD}
  - {SOURCE_RELATION: fb, DATE_DAY: '2026-07-01', ACCOUNT_ID: 1, CURRENCY: EUR}
- table: facebook_ads__ad_report
  rows:
  - {SOURCE_RELATION: fb, DATE_DAY: '2026-07-01', ACCOUNT_ID: 1, AD_ID: 10, SPEND: 100, CLICKS: 7}
expect:
  columns: [platform, union__currency, sum__spend, sum__clicks]
  rows:
  - [facebook_ads, EUR, 100.0, 7]
  - [facebook_ads, USD, 100.0, 7]
```

- **`table`** is a table the warehouse holds, by its database dataset's name or as `DB.SCHEMA.TABLE`. Kater's own models read from these, so the rows go through every model between the table and the one you test.
- **A row names only the columns that matter.** Each value is cast to the column's type, as the database dataset declares it, and a column a row leaves out is NULL.
- **Every other table the test reads is empty,** so no warehouse data reaches the answer. The run still goes to the warehouse, which reads none of its tables, unless you run it `--local`.
- **A fixture expects rows.** A table given rows that no side reads is refused, since its rows change nothing.

The example pins a real problem: an account that reports two currencies on one day joins every ad row to both, so its spend is counted twice.

## Tests of a connection workspace

A connection workspace can be read by stakeholders when grants allow it, so its root's parameters and filters can be tested too. Its tests live in the workspace, in `workspaces/<workspace>/tests/<root>.sql`, named after the root, and read it the way a stakeholder's query there does:

```sql
---
name: july_2026_spend_through_reporting_date
model_type: test
as_of: '2026-08-01'
inputs:
  parameters:
    reporting_date: 1
  filters: [reporting_date]
expect:
  columns: [platform, union__currency, sum__spend]
  rows:
  - [facebook_ads, USD, 63963.91]
  - [google_ads, USD, 110747.05]
  - [tiktok_ads, USD, 12535.44]
---

SELECT
  ad_reporting.platform,
  ad_reporting.union__currency,
  ad_reporting.sum__spend
FROM workspace.ad_reporting AS ad_reporting
GROUP BY
  ad_reporting.platform,
  ad_reporting.union__currency
```

- **`inputs`** gives what a stakeholder's run gives: each parameter's value, and the optional filters to turn on. A required filter is always on, and a parameter left out takes its default.
- **The fixed row set can come from the root.** Here the `reporting_date` filter, the last 1 complete month, holds the date to July 2026, so the SQL needs no range of its own. Its window is relative, so `as_of` pins it.
- **A picker writes a query in the workspace,** reading its root, and SQL reads `workspace.<root>`. A `same_as.sql` can read the root or any shared model.
- **Save as test** asks where the test goes: your own tests of the shared model the root reads, the shared layer's file if you edit the shared models, or, from a query in a connection workspace, the workspace itself.

`kater test generate` scaffolds only shared models' tests. A workspace's show what its root adds on top of the model, so they're written by hand or saved from a query.

## How answers compare

- **Columns compare by position.** Each name in `columns` must be that output's field name, as the writer spells it: `sum__spend`. A reordered SELECT fails with "Column 4 is sum__clicks, expected sum__spend."
- **Numbers compare at the precision a stakeholder sees:** each field's `@format`, cents for `currency(…, 2)` and whole numbers for `number(0)`. Without a format, relative 1e-9 applies. `tolerance` overrides both for a column.
- **Order matters only when the query orders.** Without ORDER BY, rows compare as a multiset, and a row that matches none is paired with its closest row, so a moved number shows as that one cell. With a LIMIT, the runner warns when the first row past the cut ties with the last row kept.
- **Dates are ISO strings; NULL is `null`.** The runner warns above 100 expected rows.
- **Values must fit the types the query declares.** On a warehouse run, each value must fit the physical type its output declares, as the query's own run keeps it. A value in another type fails the test with "Query values do not fit amount's declared physical type.", even when it reads the same, such as a `ROUND` that returns a DOUBLE where the output declares DECIMAL(18,0). A local run doesn't check this, because DuckDB's types stand in for the warehouse's there.
- **`same_as` compares data, not SQL.** Both sides become CTEs of one warehouse query, joined on `key` with a FULL OUTER JOIN, which returns only the rows that differ. Columns pair by name, and a column on one side only is ignored. The other side's values are cast to the Kater field's type. Keys must be unique on each side. Neither side takes a LIMIT unless every side orders by the same fields.
