---
name: workflows
description: "Workflows. Use it when the person asks for: \"Each Friday, check …\", \"Brief me each Monday\", a watch on a query that no chart draws, or an app's weekly snapshot: steps or a check Kater runs for the person on a schedule or when asked."
---

This skill is Kater's guide `vfs://guides/workflows.md`.

# Workflows

A workflow is a prompt that Kater runs as its owner, on a schedule or when they ask, with the organisation's model key for scheduled workflows. A watch is the one kind that runs no prompt: Kater checks its metric with code. The owner reads what each run made in their inbox and on the Workflows page. Nobody sets up a task in their own agent. Read this before you make, change, run or explain a workflow.

An analysis or a build that a person asks for, such as a [deep analysis](vfs://guides/deep-analysis.md), is a skill, not a workflow.

## What a workflow is

- **Its file.** One Markdown file in its owner's folder, `connections/<connection>/users/<email>/ai_tools/workflows/<name>.md`, with a frontmatter (`label`, `name`, `description`, `schedule` with `cron` and `timezone` when it runs on its own, `paused` while it is paused, with `paused_by`, the user id of who paused it, which Kater writes, `writes` for a snapshot, and `kind` with `brief` or `watch`) and its steps. Only its owner changes it, and Kater runs it as them. A shared workflow, in a connection's `ai_tools/workflows/`, is the organisation's template that nobody runs: a person makes their own copy.
- **Its steps.** Numbered steps in plain sentences. Each step names the Kater object it works with as a mention, `@[Name](<kind>:<ref>)`, with its kind first: an app by its folder from the content root (`app:connections/<connection>/users/<email>/apps/<name>`), a project by its folder (`project:connections/<connection>/users/<email>/projects/<name>`), a workspace by its path (`workspace:<path>`), a query by its workspace path and its name (`query:<workspace path>/<name>`). Kater draws a mention as the object, with its name now, and lists the workflow on that object's page. A mention in a code span or a code block is only text: it names nothing, and a rename does not change it. So write each mention outside backticks.
- **Four kinds.** Kater ships one kind for each reason a person keeps a workflow. `kind` names a brief or a watch. Left out, a workflow whose `writes` names an app is a snapshot, and any other is general. A workflow with `writes` takes no `kind`.
  - **Snapshot.** Saves the numbers and the words of one app at each run, and the app keeps each run as a snapshot. Its frontmatter names the app in `writes`, by its folder from the content root. An app has one at most, and only a person who can edit the app makes it. `kater new app --start deck`, `report` or `digest` makes it with the app. It runs on a schedule, or with no schedule when the document keeps the numbers of one day, and it may post in one Slack channel. Its draft waits in its owner's inbox, and the kept snapshot goes to the app's readers and each project that lists the app.
  - **Brief.** `kind: brief`. Writes a page and an answer for its owner each time, as Monday Brief does: the page in the run's topic in their brainstorm folder, laid out by the digest template (vfs://guides/digest.md), and the answer in their inbox. Its template is [Monday Brief](vfs://guides/monday-brief.md), and [digest](vfs://guides/digest.md) writes a person's own from it.
  - **Watch.** `kind: watch` and a `watch` block, and it must have a schedule. Checks one metric against its line and tells its owner only when it crosses. [Watch](vfs://guides/watch.md) drafts the line with the person, and their yes arms it. Kater writes its steps from its block.
  - **General.** Runs any steps the person writes, on a schedule or when they ask, with its answer in their inbox. Use it for a check or a reminder that mentions apps, projects, workspaces or queries. A scheduled run reads, and writes only new queries in its owner's own workspaces and files in its owner's brainstorm folder. It refuses a step that changes anything else or opens a tab.
- **A watch's block.**

  ```yaml
  kind: watch
  schedule:
    cron: "0 8 * * *"
    timezone: America/New_York
  watch:
    query: connections/<connection>/users/<email>/workspaces/paid_media/roas_by_week
    field: roas
    direction: below
    line: 3
    slack: true
  ```

  `query` is a workspace query: its workspace's path from the content root, then its name. `field` is the field it checks, `direction` is `below` or `above`, `line` is a number, and `slack: true` also sends a Slack DM. Each run reads the query's first row as the owner, so the query returns one row per period, newest first. It compares `field` with `line` and calls no model, so it needs no model key. Only the run that crosses opens an inbox row, "<label> crossed the line". A run that stays past the line opens no new row, and a run inside it says "Under the line" on its card.
- **Who reads it.** Its owner, and each person who can read the file. The app's Workflows tab, the project's rail and the Workflows page (`/workflows`) list the workflows a person can read.
- **Its runs.** Each time it runs is a run: on its schedule, by Run now, or by Try again. A snapshot workflow's run leaves a draft that its owner reviews, a brief's or a general workflow's run sends its answer to its owner's inbox, and a watch's run tells them only when it crosses. A run that failed or was skipped says why. When the organisation has no model key for scheduled workflows, a snapshot run still checks the figures and asks its owner to write the words with their own agent. A watch needs no key.
- **Where it runs.** Kater runs it, unless its owner asked that their own agent's scheduler run it (`runs_in: agent`). Only then is there a task to set up, as [schedule workflow](vfs://guides/schedule-workflow.md) says under In their own agent.

## Commands

- `kater workflow list` lists the workflows the person can read, their Slack DM schedules and the shared workflows of each connection's AI tools, each with its key and its id, its state, its next run and its last run. A workflow's key is `<connection>/users/<user id>/<name>` for a person's own and `<connection>/<name>` for a shared one, and a Slack DM schedule's is `<connection>/users/<user id>/schedules/<name>`; every `kater workflow` command and `kater runs list --of` take the key or the id, never the file's path. A skill and an agent are named by their key in the same way, and `kater ai-tools list` gives each one's key and id, which every `kater` command of its kind takes. `--app <folder>` lists an app's snapshot workflow and each workflow that mentions it; `--project <folder>` lists the snapshot workflow of each app of the project and each workflow that mentions the project.
- `kater workflow show <key>` gives its facts, its steps, the objects its mentions name, who runs it, whether it is paused, its next and last run and, for its owner, which model key writes its words (`key_words`).
- `kater new workflow "<Name>" --start snapshot|brief|watch|general [--app <folder>] [--cron <when> --timezone <zone>] [--channel <id>] --file -` makes one in the person's own folder, in one commit. Send the steps on stdin, with a mention for each object. `--cron` takes `day`, `weekday`, `monday`, `friday`, `month` or any cron.
- A watch takes no `--file`: `kater new workflow "<Label>" --start watch --metric <query>.<field> [--direction below|above] --line <number> --cron <when> [--timezone <zone>] [--alert inbox|slack]`. `--metric` is the query from the content root, then `.field`. `--direction` is `below` when left out, `--alert` is `inbox`, and a watch must have `--cron`.
- `kater workflow save <key> [--label …] [--cron … --timezone … | --no-schedule] [--runs-in kater|agent] [--channel <id> | --no-channel] [--file -]` changes the person's own workflow. What you leave out stays. For a watch, `--metric`, `--direction`, `--line` and `--alert` change its block, such as `kater workflow save <key> --line 2.5`.
- `kater workflow duplicate <key> --label <name>` copies a workflow the person can read, their own, one shared with them or a shared workflow of a connection's AI tools, into their own folder: the kind, the steps, a watch's block, the schedule and who runs it. The copy runs as them, and its schedule starts paused, so it runs only after `kater workflow resume <key>`. A snapshot workflow is not copied, since an app has one snapshot workflow.
- `kater workflow run <key>` runs the person's own workflow now and prints the run's id. A second one while a run goes prints that run's id.
- `kater workflow pause <key>` and `kater workflow resume <key>` stop and start its schedule. A pause writes `paused: true` and `paused_by`, your user id, so the admin's Scheduled workflows says who paused it. A resume removes both, and it does not run the times it skipped.
- `kater runs list [--of <key>]` lists the person's runs, the ones that need them first, each with what it made and the address of each thing. `kater runs show <run>` reads one, and `kater runs retry <run>` tries a failed or skipped one again.
- Under `kater`, the reads run at once. `kater new workflow`, `duplicate`, `run`, `save`, `pause`, `resume` and `kater runs retry` change what Kater does, so run them with `kater_apply` after the person says yes.

## Making one

1. Work out what each run does, and which objects it works with. Find each object's ref: `kater app list`, `kater project list`, or the workspace's path.
2. Select the kind. "Save this app's numbers each week" is a snapshot workflow on that app. "Brief me each Monday" is a brief: follow [digest](vfs://guides/digest.md). "Tell me when ROAS drops below 3" is an alert when a chart draws the figure, with no workflow: see [pin and alerts](vfs://guides/pin-and-alert.md). For a query that no chart draws, or a check at a time of their own, it is a watch: follow [watch](vfs://guides/watch.md), which adds the metric's query to the person's workspace first. Anything else is a general workflow.
3. Write its steps, one sentence each (a watch has none), with a mention for each object, such as `1. Open @[TikTok budget test](project:connections/<connection>/users/<email>/projects/tiktok_budget_test) at a daily budget of $1,500.` A snapshot workflow's steps are `kater snapshot draft` on the app, the words from the packet, then `kater snapshot save`: see vfs://guides/projects-documents.md.
4. Show the person its name, its kind, its steps and when it runs, in their words ("Every Friday, 9:00 AM New York time", or "Once, now" for a snapshot workflow with no schedule). Make it with `kater new workflow` only after they say yes.
5. When it has a schedule, Kater runs it from then on. Say when it runs next and where its result arrives, and offer to run it now with `kater workflow run <key>`. Never set up a task in the person's own agent unless they ask that their agent run it ([schedule workflow](vfs://guides/schedule-workflow.md), In their own agent).

## Changing one

Change the person's own workflow with `kater workflow save`, and pause or resume it with `kater workflow pause` and `resume`. Kater follows the change at the next run. A workflow that runs in their own agent (`runs_in: agent`) also needs the new prompt in its scheduled task: the answer gives it. To run another person's workflow at another time or with other steps, copy it with `kater workflow duplicate` and change the copy, or make one of their own that mentions the same objects. A shared workflow, under `connections/<connection>/ai_tools/workflows/`, changes for everyone only through the data team.

## Talking about one

Name a workflow by its name, and each object it works with as a mention. Say when it runs in words and in the workflow's time zone, never as a cron. Say that Kater runs it, and that its result arrives in the person's inbox. For a watch, say that Kater checks the metric at each time and tells them only when it crosses the line. To say why a run failed or what it made, read it with `kater runs show <run>`.
