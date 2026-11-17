---
name: check
description: "Prove a number or a query before it is shared. Use it when the person asks: check this number, why do two numbers disagree; or about SQL they wrote, such as a file or query of their own: check it against our definitions, are these definitions right, is this the same as our definitions, can I reuse anything."
---

This skill is Kater's guide `vfs://guides/check.md`.

# Check a number

Prove a number or a query before it is shared. Same or different is the warehouse's verdict (`kater diff`, a test's run), never your own reading of rows.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

Use this when the person doubts a number, sees two that disagree, or wants proof before they share one. When the number is in SQL the person wrote (a file of theirs open, or a query pasted), and they ask whether it is right, matches our definitions or can reuse something we have, follow [A query written outside Kater](#a-query-written-outside-kater). To explain a definition without checking a figure, use [explain](vfs://guides/explain.md). To keep a checked answer from drifting, use [test model](vfs://guides/test-model.md).

## Procedure

1. **Find the number,** from their words or the page they have open: `kater here` names their tabs and each chart's and table's answer, and `kater answer ID` reads the rows they see. Pin down its query, its inputs (filters, parameters, dates), its field and its row. When you cannot, ask one question.
2. **Open a topic,** `brainstorm/check_<slug>.md`, with its title, `Status: open`, the number and where it was seen.
3. **Read its definition** with `explain_field`, with the run's `result_id` when there is one. Note its model, formula, filters, grain and breadcrumbs: its start, then each step in order. Use the run's card, `kater model show REF --result ID`, for its inputs, filters with their values and dates, and warehouse SQL.
4. **Compute it again** at the same inputs, in `brainstorm/check_<slug>/checks.sql`:
   - **Statement 0:** the same field, read from the shared model it comes from, at the query's grain and with its filters.
   - **Statement 1:** the same figure from the warehouse table under that model, in native SQL written from the definition. This needs edit access to the shared models; without it, leave it out and say so.
5. **Diff each step** with `kater diff`, each side by its full path: the original query's file against `brainstorm/check_<slug>/checks.sql#0`, then `checks.sql#0` against `checks.sql#1`. Record each verdict in the note, never the rows.
6. **Report.** One line: it holds, or where it parts, such as "Same as the shared model, and the model matches the table." Then the diff. A difference is named, not explained away: say which step differs, the definition's part that could account for it, and what would settle it.
7. **Close it:** set the note's `Status:` to `answered` with the verdict. Offer to keep the check as a test with `/test-model`, or, when the shared model is what is off, to tell the data team.

## A query written outside Kater

A query the person wrote, run in the warehouse as written, such as a file of theirs that is open or a query they pasted, is checked against the shared model it reads, with a dry-run test: it keeps no test, and the statement stays in their Sandbox. Their words may name neither Kater's tests nor a model: "can you check if these definitions are right", "is this the same as ours" and "can I reuse anything" are this procedure. The test is the one of [tests writing](vfs://guides/tests-writing.md) and [tests running](vfs://guides/tests-running.md), which have its options. Reading the shared definitions beside the query is not the check: only the test's answer says whether each column holds, so run it before you answer.

1. **Read the query** the person points at: note its tables, its grain, its dates and what each column claims to be. Ask nothing the query and the catalog answer.
2. **Look for what already answers it.** `search_catalog` for the shared model over its tables, and `list_accessible_workspaces` for a workspace whose question is the same (its name, its description, its queries): a search of the query's own words misses a workspace named for the question. Name each you find: it is the answer to "can I reuse anything", and the person may want it in place of the query.
3. **Pair each column** with the field it claims to be. Make a scratch file in the person's Sandbox (`kater new scratch --name NAME`) and write the query into it with `write_file`, each column named `AS` the field of the shared model it claims to be, with the dates and filters unchanged. Write the Kater SQL of the same question, from the model's fields, to send on stdin.
4. **Run the dry-run test:** `kater new test MODEL NAME --sql-file - --same-as-native PATH#N --key COLUMN --dry-run`, with PATH#N the scratch statement. Its answer is the proof: the run's `status`, and its `diff`, which is the answer of `kater diff`: `columns` (each compared column with its `home`, which says whether the shared layer has it, and the columns of one side only) and `differences`, each cell or row that differs, with both values, the Kater SQL on the left and the plain query on the right. The answer's `message` says that no test was kept and that the plain query stays in the Sandbox. Read it; never compare rows yourself.
5. **Answer column by column.** Each column matches, differs (how, with both values: which formula is the data team's) or has no definition in the shared model: the answer to "can I reuse anything" for that column. Say what was not compared, and say plainly when the query's own number is the one the data team does not use.
6. **Offer the next steps,** each in a line, and do none before the person says yes: switch the query's formula to the shared one and run the test again; keep the check as a test of their own (the same line without `--dry-run`, through `kater_apply`): it holds their SQL as it was that day and fails when the shared layer's answer moves, and it does not follow later edits to their file; reuse the workspace or query of step 2; add what the shared model lacks to a workspace and ask the data team to promote it. Write nothing to their file unasked. Name keeping the check as a test, and asking the data team for a column the shared model lacks, in every list of next steps; when the switched query's test passes, say so and offer those two again, since the proof of the switch is what lasts.

## Stop conditions

- The number came from outside the warehouse, such as a spreadsheet: say what the Kater query returns for the same definition and inputs, and that the tool cannot compare the two.
