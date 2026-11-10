---
name: check
description: "Trace one number back to its source and prove each step. Use it when the person asks: check this number, why do two numbers disagree."
---

This skill is Kater's guide `vfs://guides/check.md`.

# Check a number

Trace one number back to its source and say whether it holds. Same or different is `kater compare`'s verdict, never your own reading of rows.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

Use this when the person doubts a number, sees two that disagree, or wants proof before they share one. To explain a definition without checking a figure, use [explain](vfs://guides/explain.md). To keep a checked answer from drifting, use [test model](vfs://guides/test-model.md).

## Procedure

1. **Find the number,** from their words or the page they have open: `kater here` names their tabs and each chart's and table's answer, and `kater answer ID` reads the rows they see. Pin down its query, its inputs (filters, parameters, dates), its field and its row. When you can't, ask one question.
2. **Open a topic,** `brainstorm/check_<slug>.md`, with its title, `Status: open`, the number and where it was seen.
3. **Read its definition** with `explain_field`, with the run's `result_id` when there is one. Note its model, formula, filters, grain and breadcrumbs: its start, then each step in order. Use the run's card, `kater model show REF --result ID`, for its inputs, filters with their values and dates, and warehouse SQL.
4. **Compute it again** at the same inputs, in `brainstorm/check_<slug>/checks.sql`:
   - **Statement 0:** the same field, read from the shared model it comes from, at the query's grain and with its filters.
   - **Statement 1:** the same figure from the warehouse table under that model, in native SQL written from the definition. This needs edit access to the shared models; without it, leave it out and say so.
5. **Compare each step** with `kater compare`, each side by its full path: the original query's file against `brainstorm/check_<slug>/checks.sql#0`, then `checks.sql#0` against `checks.sql#1`. Record each verdict in the note, never the rows.
6. **Report.** One line: it holds, or where it parts, such as "Same as the shared model, and the model matches the table." Then the comparison. A difference is named, not explained away: say which step differs, the definition's part that could account for it, and what would settle it.
7. **Close it:** set the note's `Status:` to `answered` with the verdict. Offer to keep the check as a test with `/test-model`, or, when the shared model is what's off, to tell the data team.

## Stop conditions

- The number came from outside Kater, such as a spreadsheet: say what the Kater query returns for the same definition and inputs, and that the tool can't compare the two.
