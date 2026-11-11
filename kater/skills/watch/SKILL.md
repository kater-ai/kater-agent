---
name: watch
description: "Watch a metric that no chart draws against a line. Use it when the person asks: tell me when a metric crosses a line."
---

This skill is Kater's guide `vfs://guides/watch.md`.

# Watch a metric

Draft a watch on one metric: the line, the query that answers the metric, and how often it is checked. A line is a parameter: profile it, offer candidates, and record the choice as provisional. Nothing is armed without the person's yes.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

A chart's figure does not need this. Alert me when on the chart, or `kater alert add QUERY --figure FIELD[:ROW]`, opens on the recommendation with no profile to write and no workflow to make, the alert is checked each night and tells them in their Inbox, and in Slack, email or the Daily brief when they select them, and the chart draws its level (vfs://guides/pin-and-alert.md). A watch is for what that does not cover.

A watch is a workflow of its own kind (`kind: watch`, [workflows](vfs://guides/workflows.md#what-a-workflow-is)). At each time in its schedule, the watch reads the first row of its workspace query, compares one field with the line, and tells its owner in their inbox only on the run that crosses, with a Slack DM too when they chose it. It calls no model, so it needs no model key, and there is no task to set up in their agent.

## When to use

Use this when the person wants to hear when a metric crosses a line and no chart draws it, or when they want it checked at a time of their own, such as each hour. When a chart draws the figure, set an alert: run `kater alert recommend QUERY --figure FIELD[:ROW]`, show them the recommendation, and set it with `kater alert add`. For a regular update whatever happens, use [digest](vfs://guides/digest.md).

## Procedure

1. **Open a topic,** `brainstorm/watch_<metric>.md`, with its title and `Status: open`.
2. **Find the metric's verified definition,** as [brief me](vfs://guides/brief-me.md) does, and the grain it's checked at, such as daily or weekly.
3. **Set the line.** Profile the metric's history at that grain with a statement whose percentiles and counts the statement computes, in `brainstorm/watch_<metric>/profile.sql`, run with `kater run --select FILE`. Offer three candidates from what it returns, sensitive, balanced and conservative, each with how often it would have crossed. A line they gave is checked the same way. Record the choice in the note as provisional (vfs://guides/planning.md, Parameter calibration).
4. **Draft the metric's query:** a statement in the same file that returns one row per period at the check's grain, newest first, with the metric as a field, such as `roas` by week. The watch reads only its first row, so the order matters. Run it over the history and show the periods past the line, which are the times it would have told them.
5. **Ask once,** with the metric, the line and its direction, how often it is checked, such as every day at 8:00 AM in their time zone, and whether they want a Slack DM as well as the inbox row.
6. **After a yes, arm it:**
   - Add the metric's query to their own workspace: `kater add-to brainstorm/watch_<metric>/profile.sql --statement N --to workspace:WORKSPACE`. The answer names the query. The watch names it by its workspace's path from the content root, then its name, such as `connections/<connection>/users/<email>/workspaces/paid_media/roas_by_week`. Their own folder is `connection.folder` of `kater_start`. Through the Kater MCP, the query is saved as it's made. In a local copy, commit it and publish it with `kater git push -m "..."` first: the watch checks the published query, not your copy.
   - Make the watch: `kater new workflow "<Label>" --start watch --metric <query>.<field> --direction below|above --line <number> --cron day --timezone <zone> --alert inbox|slack`. `--cron` takes `day`, `weekday`, `monday`, `friday`, `month` or a cron at the check's grain. A watch has no steps to write: they are written from its metric and line.
   - Set the note's `Status:` and record where the watch is.
7. **Say what happens next:** the watch checks the metric with code at each time and tells them only when it crosses the line. A run that stays past the line sends nothing new, and a run inside it says "Under the line" on its card on the Workflows page. `kater workflow run <key>` checks it now. To move the line later, `kater workflow save <key> --line <number>`, and `--metric`, `--direction` and `--alert` the same way ([schedule workflow](vfs://guides/schedule-workflow.md)).

Without a yes, it stays drafted, not armed, and you say so. Never say a watch is armed until `kater new workflow` answers with its path.

To get a query's figures at every set time, whatever they are, a Slack DM schedule sends them (vfs://guides/schedule.md). It is not a watch: it sends at each time, also when nothing crossed.
