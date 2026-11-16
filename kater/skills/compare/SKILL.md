---
name: compare
description: "One metric side by side for two periods, and what moved it. Use it when the person asks: compare this month with last month."
---

This skill is Kater's guide `vfs://guides/compare.md`.

# Compare two periods

Put one metric's two periods side by side, overall and by the dimensions that moved it most. The check computes the change and each statement of a split computes its own; you never subtract one row from another.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

Use this when the person asks how a period compares with another, such as this month against last month or last year. For several metrics at once, use [brief me](vfs://guides/brief-me.md). To find out why it moved, hand over to [deep analysis](vfs://guides/deep-analysis.md).

## Procedure

1. **Run the check.** `kater compare --query <workspace>/<query> --periods <A>,<B>`, where the query is the query that returns the metric unchanged (a verified definition, as [brief me](vfs://guides/brief-me.md) finds it) and A and B are the two periods, each by its first day, such as `2026-09-28,2026-10-05` for two weeks. `--grain month` sets months. Without `--periods` they are the last complete period and the one before. `--workspace <workspace>` checks every query of a workspace, and `--app <app>` an app's figures.
   - **The answer is the verdict:** each figure with its two values, its change and the sentences of its checks, the figures that stand out first. Every value and change you report comes from it, never from your own subtraction.
   - **Not checked yet:** it starts the check and waits for it. Through the MCP, a check that has not run goes through kater_apply after the person's yes.
   - **No query returns the metric:** write one with `kater query new` in one of the person's own workspaces, as [brief me](vfs://guides/brief-me.md) does, then run the check on it. A metric that has only an unverified definition is checked only when the person agrees, and the answer says it is unverified.
2. **Say the periods** as exact dates of the same length. The check reads complete periods: when one is still under way, say so and report the last complete one.
3. **Split the change by a dimension,** when the person asks what moved it or the verdict shows a change. Write the statements in `brainstorm/compare_<metric>/compare.sql`, opening the topic's note, `brainstorm/compare_<metric>.md`, with its title and `Status: open`: one statement per dimension, at most three that the model offers and the person would split by, such as channel or platform, each the same comparison by that dimension computed with a calendar comparison (vfs://guides/period-comparisons.md), ordered by the size of the change. A statement never decides whether the metric changed: the check does.
4. **Run them** with `kater run --select FILE`.
5. **Show it.** One line for the overall change, from the check, then the dimension that moved it most as a table, from the run. Say plainly when a run was truncated or failed. A split that explains the change is a hypothesis until `/deep-analysis` tests it.
6. **Record it** in the note: the periods, what ran and `Status: answered`, never a figure.
7. **Offer next:** "Why did it change?" (`/deep-analysis`), or, after a yes, keep a statement as a workspace query with `kater add-to FILE --statement N --to workspace:WORKSPACE`.
