---
name: brief-me
description: "What moved in the numbers the person follows since a date or their last brief. Use it when the person asks: brief me, what changed, how are things going."
---

This skill is Kater's guide `vfs://guides/brief-me.md`.

# Brief me

A short brief on what moved in the numbers the person follows. Every figure comes from a verified definition, and every change comes from the check, `kater compare`, never from you.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

When the person asks what is in their brief, for their Daily brief, or what stands out today, run `kater brief` and answer from it: it is ranked each night, so one call answers it. Lead with its one thing, then what moved, the projects they follow, what needs them and their figures, and stop there.

Use this skill when the person asks what changed over a window, how things are going, or for a brief since a date. To find out why something moved, hand over to [deep analysis](vfs://guides/deep-analysis.md). To set two periods of one metric side by side, use [compare](vfs://guides/compare.md). For a brief on a schedule, use [digest](vfs://guides/digest.md).

## Procedure

1. **Run the check.** `kater compare --workspace <workspace>` for the workspace of each number they follow, or `--app <app>` for an app they follow and `--query <workspace>/<query>` for one query. `kater brief` names each figure with its workspace query. Without `--periods` it compares the last complete period (a week, where the checks offer it) with the one before; `--grain month` (or day, quarter, year) and `--periods <A>,<B>`, each by its first day, set the window and the one before it. Its answer is the verdict: each figure with its two values, its change and the sentences of its checks, the ones that stand out first. Every value and change in the brief comes from it, never from your own query or subtraction. When the periods are not checked yet, it starts the check and waits for it; through the MCP, a check that has not run goes through kater_apply after the person's yes.
2. **Read what they follow,** with `kater brief`. Its `figures` are the numbers they follow and its `recommended` are the ones selected for them, each with its `field` as `holder.field` and the workspace query it comes from. Its `projects` are the projects they follow. With no metric, ask for two or three, follow them as [about me](vfs://guides/about-me.md) says, then go on.
3. **Set the window.** From `since` or their words, as whole periods of one grain. Left out, it starts on the date of their last brief in `brainstorm/brief-me.md`, or a week ago when there is none. It is compared with the window of the same length just before it, which is what the check read. Say both windows.
4. **Find each metric's verified definition:** a field `explain_field` reports verified, read from the shared models or from a query that returns it unchanged. A metric without one is left out of the brief and named at the end, and so is a metric the check has no row for, with the reason it gives.
5. **Brief them.** Lead with the biggest moves, at most five, a line each: the metric, its value, the change and its direction, and its series key from the check. Put the metrics that held steady in one line. Then each project they follow, a line each, with its state from `kater brief`. Say plainly when a check was not ready or failed. Never call a move a cause.
6. **Split a move** by a dimension only when the person asks where it came from: one query for it in one of their own workspaces, written with `kater query new`, with the change computed in the query with a calendar comparison (vfs://guides/period-comparisons.md), and run with `kater run --select QUERY`. A split never decides whether a metric changed: the check does.
7. **Record it.** In `brainstorm/brief-me.md`, add a line for this brief: its date, its two windows and the command that ran, never a figure. A new note opens with `# Brief me` and `Status: answered`.
8. **Offer one next step** for the biggest move: "Why did it change?" (`/deep-analysis`), or `/compare` by a dimension.

## Stop conditions

- No metric has a verified definition: say which lack one, and offer [build a query](vfs://guides/build-query.md) to define the first.
