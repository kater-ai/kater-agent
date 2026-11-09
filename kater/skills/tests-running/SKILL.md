---
name: tests-running
description: "Tests of shared models: starting, recording and running. Use it when the person asks for: A check that a shared model still gives the numbers it should."
---

This skill is Kater's guide `vfs://guides/tests-running.md`.

# Tests of shared models: starting, recording and running

A part of [Tests of shared models](vfs://guides/tests.md).

## Starting a suite

`kater test generate semantic.ad_reporting` writes a starting suite: it picks queries from the model's own menus, runs each once, and appends them to your own test file of the model as drafts, through the writer. `--shared` grows the shared layer's file instead.

```sh
kater test generate semantic.ad_reporting --dry-run
kater test generate semantic.ad_reporting --shared
kater test generate semantic.ad_reporting --count 12
kater test generate semantic.ad_reporting --question "What was spend by platform last month?"
kater test generate semantic.ad_reporting --window union__date_day=2026-07-01..2026-07-31
```

- **5 tests by default, one of each shape:** picker → rows, picker + Kater SQL → rows, Kater SQL → refused, picker with operations → rows, and picker → same as native SQL. A shape the file already has isn't written again, and a shape the model can't take, such as a refusal on a model with no rules, is skipped.
- **The native side is left empty.** Kater never writes native SQL: that test reports `incomplete` until you write it, pointed at whatever you trust, such as the dbt mart.
- **`--count N`** takes fewer shapes in order, or adds coverage picks: each the query that covers the most of what's still uncovered, which is every measure, grain, kind of dimension (date, category, name, ID, raw number), operation family and declared rule. Ties go to the coarser grain, then fewer fields. It never sweeps every combination.
- **One settled window.** Every pick reads the same closed range: the one the file's tests already read, or else the latest complete month that ended at least 30 days ago.
- **Deterministic.** A rerun on an unchanged model picks the same queries and writes nothing it already wrote.
- **A pick that fails is reported, not written:** a stakeholder picking the same thing would hit the same error. The report is kept, and `kater test readiness` lists it as known issues. A draft worth a close look is listed under "check before accepting", such as a name that's NULL on every row, or a day of week that returned more rows than it has values.

Every draft has `draft: true` and no `description`: only you know what you checked its numbers against. Check each one, add a `description`, then `kater test update REF NAME --checked`. When your own file and the shared one both have a test of that name, `--file PATH` says which.

**`--from-questions`** also turns each of the model's `business_questions` into a picker test, through the generation Explore uses: a language model proposes the `query.new` request that answers it, and the writer writes, compiles and runs it once, held to the same window. Each draft keeps its `question`, so a rerun skips the questions the file already answers. A question the model's fields can't answer, or whose query fails, is reported, not written. It needs the model provider Explore uses, such as the local Codex companion. `kater test readiness` says how many of the model's questions a checked test answers, which informs and never blocks hand-off.

## Editor help

Nobody has to write picker YAML by heart. In a test file the editor:

- **completes** every key a test takes, and every key of `picker` and `expect`; the model's fields after `field:`; only the operations that field's menu offers after `operation:`; each parameter from the operation's own schema, with its choices; the fields the block shows for `order_by`, `partition_by` and `same_as.key`; real values for `is_one_of`; and the latest settled month for `range`. Inside `native: |` it offers the connection's tables and columns, and the Kater outputs to name after `AS`. On an empty line, "New test" writes a block with a date range.
- **flags each problem on its line** as you type: an unknown field, an operation the field doesn't offer or `query.new` doesn't take, a missing or different fixed range, a native alias that matches no Kater output, and, once compiled, whatever the sandbox compile finds.
- **shows the SQL the writer writes for a picker** when you hover it.

`kater model inspect PATH --feature completions --at LINE:CHARACTER` and `--feature diagnostics` give the CLI and the Developer agent the same help. `--feature tests` lists each test as the file's own parse reads it: its range, paths, expectation, and where each expected cell is written, which the Sandbox's Test Suite reads rather than parsing the file itself.

## Recording answers

Answers are recorded, not typed. Recording writes numbers at their format's precision, so the warehouse's `48947.020000000004` is kept as `48947.02`, and puts each row on one line.

Each of these adds to your own `users/<email>/tests/`, to the file whose tests already name the model, else `<model>.sql`; add `--shared` to add to the shared layer's.

```sh
kater new test semantic.ad_reporting NAME --sql-file totals.sql --record
kater new test semantic.ad_reporting NAME --sql-file totals.sql --record --shared
kater new test semantic.ad_reporting NAME --picker-file request.json --record --draft
kater new test semantic.ad_reporting NAME --sql-file spend_by_platform.sql --refused
kater new test semantic.ad_reporting NAME --sql-file by_month.sql --same-as-native mart.sql --key platform --key union__date_day
kater new test semantic.ad_reporting NAME --from-query connections/CONNECTION/workspaces/ad_reporting/top_campaigns --record --draft
kater new test semantic.ad_reporting NAME --sql-file start.sql --draft
kater test update semantic.ad_reporting NAME --checked
```

`--from-query` saves a workspace query as a test of the model its workspace's root reads, with its SQL reading that model. Only a root that reads one shared model whole, `SELECT *` with at most optional filters, reads what a test reads, so any other root is refused.

```sh
kater new test workspace.ad_reporting NAME --sql-file by_platform.sql --inputs-file inputs.json --record
kater new test semantic.ad_reporting NAME --sql-file by_currency.sql --given-file rows.json --record
kater new test semantic.ad_reporting NAME --picker-file last_two_weeks.json --as-of 2026-08-03 --record
```

`--workspace` writes a test of a connection workspace's root into its own test file, and `--from-query` there takes one of its queries as written. `--inputs-file`, `--given-file`, `--as-of` and `--question` set those keys, and `--record` runs the test with them.

`--record` runs the test once and keeps its rows; with a picker and SQL both, they must agree. A `--draft` with none of `--record`, `--refused` or `--same-as-*` is a test to finish, as Files' New test starts one: write its SQL, and its first run that returns rows records them. `--refused` compiles the SQL and keeps the code the compiler refuses it with. After an intended model change, `kater test --update` re-records failing rows tests and prints each number that moved, so a reviewer approves numbers, not only SQL. It never touches `same_as` or `refused` tests.

Every block is written with the SQL writer's frontmatter serializer, and saved through the write path, which formats its SQL and refuses a save that breaks a test: a path that doesn't compile, a missing range, or a malformed key. A draft saves as you write it: what keeps it from running is a warning until it's done. Recording or checking a draft changes only that block's `expect` or `draft`, through `source.patch`, and leaves every other line, comments included.

## Running tests

```sh
kater test --select semantic.ad_reporting
kater test --select path:connections/CONNECTION/modeling/kater_objects/tests/ad_reporting.sql,july_2026_totals_by_platform --show-sql
kater test --select state:modified+ --state git:main --compile-only
kater test --select connection:CONNECTION --stale
```

Through the Kater MCP, `kater` runs `kater test --compile-only`, and each other `kater test` run goes through `kater_apply`. A run compiles every selected test in the sandbox, then runs what needs the warehouse, at most 4 at a time. Without `--select`, it selects every test file of the connection you are in that you can read: the shared layer's, each connection workspace's, your own, and anyone else's that's shared with you, or for an admin everyone's. `kater test --select REF` runs every one of those files of the model, since a model's selection selects its tests. One run reads the tests of one connection: when a selection reaches more than one, add `connection:NAME` to it, as in `--select connection:CONNECTION,tag:team=marketing`. It records each run in the Kater root's `.kater/test-status/<connection>/<model>.json`, or `users/<email>/<model>.json` for a person's own file, a local record that's never committed: its status, when, how long, a fingerprint of the SQL it ran, the model's revision and a hash of what it returned.

The fingerprint is each path's compiled SQL, with the columns nothing reads pruned, its bindings, and the physical type it declares for each output. Same SQL, same types and same data give the same answer, so:

- `--compile-only` compiles, reads no warehouse, and reports each test as `unchanged` or `stale` against its last pass. Refused tests are checked in full.
- `--stale` runs only tests whose SQL, declared types or expectation changed since their last pass, or that never passed.
- `--select state:modified+ --state git:main` selects the tests of each file this branch changed since it left `main`, committed or not, and of every model that reads those files. The rest count as `unchanged`.
- A failing test whose SQL and types are unchanged since its last pass says so: the data changed.

**The merge check is `kater test --select state:modified+ --state git:main --stale`.** It compiles only the tests a change could reach, and runs in the warehouse only those whose fingerprint moved since their last pass. Refusals are always checked, since they only compile. With no run records, as on a fresh CI checkout, every changed test runs.

**An optional commit hook stays compile-only:** `kater test --select state:modified+ --state git:main --compile-only`, so it never waits on the warehouse; the shared compile store keeps its compiles warm. It fails only on a test that no longer compiles.

`kater test list` lists tests with their last status (`--failing`, `--drafts`, `--stale`), and who added each one and when, from the content store's history: the author of the commit that wrote its `name:` line. The Sandbox's Test Suite shows their initials beside each test. Exit codes follow the CLI's: `0` every test passed, `1` a test failed or can't run, `2` an invalid request. `--json` gives one item per test.

### Without the warehouse: `kater test snapshot` and `--local`

```sh
kater test snapshot --select connection:CONNECTION --dry-run
kater test snapshot --select connection:CONNECTION
kater test --select connection:CONNECTION --local
kater test --select state:modified+ --state git:main --local
```

`kater test snapshot` copies just the warehouse rows the tests read into a local DuckDB file, once, with your credentials. Then `--local` runs tests on that copy with none, such as in a commit hook.

- **What it copies.** Each table a selected test reads. A table whose rows reach the model's date field is kept to the tests' dates, by the column the compile's lineage names: for ad_reporting, 74,000 rows over its 2025-05-01..2026-08-02 tests. Any other table, such as a currency lookup a join reads, is copied whole, up to 200,000 rows.
- **Where.** `.kater/test-snapshots/<connection>/` in the Kater root: one file per database, and `snapshot.json`. It's runtime state, never committed.
- **What it answers.** Right after copying, each test runs on the copy and is compared with the answer it expects. A test whose local answer differs is one the copy doesn't answer, because DuckDB reads a shape differently or the test reads rows outside the copied dates. A local run leaves it for the warehouse and says why, rather than failing it.
- **Fixture tests run locally with no copy at all,** since they read only the rows they give.
- **A local run never stands in for a warehouse run.** DuckDB accepts query shapes Snowflake refuses, so the merge check still runs stale tests in the warehouse. A local run is recorded apart, as `last_local`, and never makes a test pass or stale. `--update` re-records only from the warehouse.

Snapshot again after a model starts reading a new table, or after tests move to new dates: a local run says which table the copy doesn't hold.

### The picker path is the field picker's

`query.new` writes a picker as the editor's field picker writes the same picks, each an `edit.model` edit, so a picker test holds the field picker's own SQL to its answer. A measure brings in the dimensions its rule holds, a query that shows a measure groups by its row fields, and a sort is by an output's position. A condition is an optional filter over a parameter whose default is the condition's value, and a run turns it on.

Each command is a named operation, `test.generate`, `test.create`, `test.run`, `test.record`, `test.status` and `test.snapshot`, so the CLI, the editor and the Developer agent run the same code.

## Ready to hand off

`kater test readiness semantic.ad_reporting` says whether the model is ready to hand to a stakeholder:

```
semantic.ad_reporting: 12 tests, last run 2026-09-27
  Tests        12 passing; 0 drafts
  Measures     5 of 5 in a checked test
  Families     aggregations, timeframes, windows, filters, sort; not yet: calculate_at, derive
  Picker path  7 tests; every picker path matches
  Oracle       matches DEMO_DB.ANALYTICS_AD_REPORTING.AD_REPORTING__AD_REPORT (45 rows), from SQL and from the picker
  Refusals     Spend without Currency, Conversion Value without Currency pinned
  Ready to hand off: yes
```

Ready means five things are true: every test's last run passed, and nothing it reads changed since it compiled; no drafts are left; every measure is in a checked test; at least one `same_as` test against native SQL passes; and every declared rule is pinned by a refused test. Known issues, what `kater test generate` couldn't write, are listed and never block it, and neither do the model's business questions no checked test answers yet, which a `Questions` line counts.

"Tested" only informs. A model's Catalog page and the workspace source picker show a chip such as "12 tests, passed Sep 27", and nothing is ever refused for a model without tests.

## Nightly runs

Golden answers can change with no commit, when upstream data is restated. A deployment job in the Kater root's `kater.config.yaml` catches that: it runs every test of one connection's shared layer and connection workspaces, right after the connection's sync succeeds. People's own tests are theirs to run, so the job leaves them out.

```yaml
jobs:
  test_snowflake:
    command: kater test
    connection: snowflake_prod_connection
    deployment: production
    after: sync_snowflake
```

- **A job has a `schedule` or an `after`, not both.** `after` names a job of the same connection and deployment. A connection has one test job per deployment.
- **The run passes** when every test passes or the connection has no tests, and fails otherwise. A failure never blocks anything.
- **The Kater panel's bell lists each model with failing tests,** such as "3 tests failing in ad_reporting", until a run where they pass. `kater test list --connection CONNECTION --failing` shows them.
- **Most of it costs nothing.** Test SQL is the same night after night, so Snowflake can answer it from its result cache while the data is unchanged.
- **A failure whose SQL and types are unchanged since its last pass means the data changed.** Accept the new numbers with `kater test --update`, or chase them upstream.

In their own terminal, an operator runs it now with `kater job run test_snowflake --kater-root ROOT --deployment production`, and `kater job run sync_snowflake …` runs the sync and then the tests. The MCP refuses `kater job`. See [Scheduled jobs](vfs://guides/kater-cli-commands-setup.md#scheduled-jobs).
