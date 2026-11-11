---
name: brief-me
description: "What moved in the person's pinned figures since a date or their last brief. Use it when the person asks: brief me, what changed, how are things going."
---

This skill is Kater's guide `vfs://guides/brief-me.md`.

# Brief me

A short brief on what moved in the figures the person pinned. Every figure comes from a verified definition and a run, and every change is computed by the query that returns it, never by you.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

When the person asks what is in their brief, for their Daily brief, or what stands out today, run `kater brief` and answer from it: it is ranked each night, so one call answers it. Lead with its one thing, then what moved, the projects they follow, what needs them and their figures, and stop there.

Use this skill when the person asks what changed over a window, how things are going, or for a brief since a date. To find out why something moved, hand over to [deep analysis](vfs://guides/deep-analysis.md). To set two periods of one metric side by side, use [compare](vfs://guides/compare.md). For a brief on a schedule, use [digest](vfs://guides/digest.md).

## Procedure

1. **Read what they watch,** with `kater brief`. Its `figures` are the metrics they pinned and its `recommended` are the ones selected for them, each with its `field` as `holder.field` and the workspace query it comes from. Its `projects` are the projects they follow. With no Daily brief, the projects are `## Projects I follow` in their own folder (`connection.folder` of `kater_start`), in `connections/<connection>/users/<email>/about.md`. With no metric, ask for two or three, pin them as [about me](vfs://guides/about-me.md) says, then go on.
2. **Set the window.** From `since` or their words. Left out, it starts on the date of their last brief in `brainstorm/brief-me.md`, or a week ago when there's none. It's compared with the window of the same length just before it. Say both windows.
3. **Find each metric's verified definition:** a field `explain_field` reports verified, read from the shared models or from a saved query that returns it unchanged. A metric without one is left out of the brief and named at the end.
4. **Run it.** For each metric, one query that returns the window, the window before it and the change between them, the change computed in the query with a calendar comparison (vfs://guides/period-comparisons.md). A saved query that already returns the comparison of a verified field runs as it is. Write each one as a query in one of the person's own workspaces with `kater query new`, and run them in one `kater run --select QUERY QUERY`.
5. **Brief them.** Lead with the biggest moves, at most five, a line each: the metric, its value, the change and its direction, and the run it came from. Put the metrics that held steady in one line. Then each project they follow, a line each, with its state from `kater brief`. Say plainly when a run was truncated or failed. Never call a move a cause.
6. **Record it.** In `brainstorm/brief-me.md`, add a line for this brief: its date, its two windows and the file that ran, never a figure. A new note opens with `# Brief me` and `Status: answered`.
7. **Offer one next step** for the biggest move: "Why did it change?" (`/deep-analysis`), or `/compare` by a dimension.

## Stop conditions

- No metric has a verified definition: say which lack one, and offer [build query contract](vfs://guides/build-query-contract.md) to define the first.
