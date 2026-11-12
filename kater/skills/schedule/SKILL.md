---
name: schedule
description: "Schedule a Slack DM. Use it when the person asks for: \"Send me this each week in Slack\"."
---

This skill is Kater's guide `vfs://guides/schedule.md`.

# Schedule a Slack DM

A schedule sends one saved query’s fresh figures to its owner in Slack. It does not run an AI model, write a digest, send to a channel, or send only when a condition changes. The Kater Slack app must be installed. The owner must be a confirmed organisation member with a connected Slack account and current access to the query and its sources.

To hear only when a metric crosses a line, make a watch instead, and to write a page again on a schedule, a brief (vfs://guides/workflows.md, [digest](vfs://guides/digest.md)). A brief's run keeps each linked figure (a `<kater-source>` with `origin="link"`, `"file"` or `"person"`) and its `read-at` as they are, never writes a new date for a figure it did not read, and names each figure it kept and the day it was read in its final message.

## Write the file

Save the file in your own folder, `connection.folder` of `kater_start`, at `connections/<connection>/users/<your email>/schedules/<name>.yaml`. Use a simple name with letters, numbers, underscores or hyphens. Use the same connection for the schedule and its query. The file is at most 16 KB. Unknown fields are refused.

```yaml
run: connections/snowflake_prod_connection/users/yvonne@kater.ai/workspaces/channel_performance_weekly/weekly_scorecard
when:
  timezone: America/Los_Angeles
  days: [monday]
  time: "09:00"
to: yvonne@kater.ai
how: slack
paused: false
```

`run` names one saved query as `<workspace>/<name>`: the workspace's folder from the content root, then the query's name as its file declares it, never the query's file. The query uses its saved defaults. `to` must be the email that owns the schedule folder. `how` is always `slack`. Give an explicit IANA time zone, such as `America/Los_Angeles`. Infer it only from the person’s known settings. Ask if it is not known. Never guess it from the server’s time zone. `days` uses full lowercase names. `time` is a quoted 24-hour `HH:MM` string. `paused` is optional and defaults to false. The file name names the schedule; do not add a `name` field.

For a fixed interval, replace `days` and `time` with `every_minutes`, an integer from 1 to 1440. Do not combine the two forms. An interval runs on UTC minute boundaries. A calendar schedule follows its local time zone. A repeated local minute at a daylight-saving change sends once. A local time that does not exist is skipped.

```yaml
when:
  timezone: America/Los_Angeles
  every_minutes: 2
```

## Arm it after approval

Draft the query and the proposed YAML first. Show the person what it runs, when it sends, and that it goes only to them. Write the schedule with `kater source write <path> --text-file -` through `kater_apply`, with the YAML in `stdin`, after their approval. Writing an unpaused schedule arms it. The `kater` tool cannot write a schedule. Slackbot’s `/mcp/read` address cannot write or arm any schedule. A local content edit or `kater git push` follows the same file contract and also arms an unpaused schedule. Never claim that a schedule is armed until the file is saved and the delivery setup is available.

The worker reads each schedule each minute and keeps one Hatchet cron for each active schedule. A save, a pause or a delete takes effect within about a minute. A restart resumes durable delivery jobs. It does not replay missed slots.

## Read, stop and resume

Each run resolves the owner’s current access, runs fresh, and sends the normal result DM. Open and Ask about this refer to the owner’s result. A result that has a chart can include its private PNG. Figures use a bounded sample. Unknown source cutoffs read `Data to null.`. `Read on` is the execution time, not the last data date. An image failure leaves the figures and result link available. A run that cannot finish sends a message with no figures and its next set time, if the Slack connection remains available. If delivery is not possible, its job and `schedule_runs` receipt keep the outcome. An uncertain external delivery never automatically posts again.

Select Stop in the DM to pause it. Only the linked owner can use that button. The pause saves `paused: true` and `paused_by`, the owner’s user id, as the owner’s content change. The confirmation’s Open History button opens `/files/<schedule path>?history=open`. Select Undo on the pause change to resume it. You can also save `paused: false`, or delete the file to remove the schedule. History records these changes. Unsent drafts, credentials and query rows never go in an address or schedule receipt.

