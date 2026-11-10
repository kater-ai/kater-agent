---
name: compare
description: "One metric side by side for two periods, and what moved it. Use it when the person asks: compare this month with last month."
---

This skill is Kater's guide `vfs://guides/compare.md`.

# Compare two periods

Put one metric's two periods side by side, overall and by the dimensions that moved it most. The statement computes every change; you never subtract one row from another.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

Use this when the person asks how a period compares with another, such as this month against last month or last year. For several metrics at once, use [brief me](vfs://guides/brief-me.md). To find out why it moved, hand over to [deep analysis](vfs://guides/deep-analysis.md).

## Procedure

1. **Find the metric's definition,** as [brief me](vfs://guides/brief-me.md) does: a verified field, read from the shared models or from a saved query that returns it unchanged. A metric that has only an unverified definition is compared only when the person agrees, and the answer says it's unverified.
2. **Set the periods** to exact dates of the same length, and say them. A period still under way is compared over the same elapsed days of the other, as the calendar comparison's `matching_elapsed` coverage does.
3. **Write the statements** in `brainstorm/compare_<metric>/compare.sql`, opening the topic's note, `brainstorm/compare_<metric>.md`, with its title and `Status: open`:
   - **Statement 0:** the metric for the period, the period it's compared with and the change, computed in the statement with a calendar comparison (vfs://guides/period-comparisons.md).
   - **One statement per dimension,** at most three that the model offers and the person would split by, such as channel or platform: the same comparison by that dimension, ordered by the size of the change.
4. **Run them** with `kater run --select FILE`.
5. **Show it.** One line for the overall change, then the dimension that moved it most as a table, from the run. Say plainly when a run was truncated or failed. A split that explains the change is a hypothesis until `/deep-analysis` tests it.
6. **Record it** in the note: the periods, what ran and `Status: answered`, never a figure.
7. **Offer next:** "Why did it change?" (`/deep-analysis`), or, after a yes, keep a statement as a workspace query with `kater add-to FILE --statement N --to workspace:WORKSPACE`.
