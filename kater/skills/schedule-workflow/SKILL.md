---
name: schedule-workflow
description: "Set up, change, pause or stop a scheduled workflow. Use it when the person asks: schedule my Monday brief, move it to 9, pause it."
---

This skill is Kater's guide `vfs://guides/schedule-workflow.md`.

# Schedule a workflow

A scheduled workflow is one file in Kater, which holds its steps and its `schedule` ([workflows](vfs://guides/workflows.md#what-a-workflow-is)). It runs at each time, as its owner, and the owner reads the result in their inbox. Nothing else is set up: no task in the person's own agent, no copy of the schedule anywhere else. There are four kinds:

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

- **Snapshot:** saves the numbers and words of one app (`writes`), and its draft waits in the owner's inbox.
- **Brief:** a page and an answer for its owner each time (`kind: brief`), as [Monday Brief](vfs://guides/monday-brief.md) writes. [digest](vfs://guides/digest.md) works one out.
- **Watch:** one metric that no chart draws, checked against its line (`kind: watch`), with an inbox row only when it crosses. [watch](vfs://guides/watch.md) works one out. A figure on a chart takes an alert instead, with no workflow (vfs://guides/pin-and-alert.md).
- **General:** any steps the person writes, with its answer in their inbox.

What each kind's file holds and every `kater workflow` command are in vfs://guides/workflows.md.

## When to use

Use this when the person wants a workflow to run on its own, or wants to change, pause or stop one that does: "schedule my Monday brief", "move it to 9", "add ROAS to it", "stop the daily watch". To work out what a new one covers first, use [digest](vfs://guides/digest.md) for a recurring brief or [watch](vfs://guides/watch.md) for a metric that crosses a line; each hands back here to schedule it.

## Procedure

1. **Find the workflow.** `kater workflow list` lists each one the person can read, with when it runs, its state and its last run; `--app <folder>` or `--project <folder>` keeps those of one app or project. A workflow with no schedule gets one. With none that fits, work out a new one with digest.md or watch.md, or write it with them in the shape below.
2. **Say where each change goes.** Each is one `kater workflow` command, and the next run follows it:
   - **Its steps,** such as another metric or a shorter brief: `kater workflow save <key> --file -`.
   - **When it runs:** `kater workflow save <key> --cron "<cron>" --timezone <zone>`. A workflow runs at most once an hour.
   - **Its name:** `kater workflow save <key> --label "<name>"`.
   - **A watch's line or metric:** `kater workflow save <key> --line <number>`, `--direction below|above`, `--metric <query>.<field>` or `--alert inbox|slack`. A watch has no steps to change: they are written from these.
   - **Pause or resume:** `kater workflow pause <key>`, `kater workflow resume <key>`. A paused workflow's times are skipped, and a resume does not run the times it skipped.
   - **Stop for good:** `kater workflow save <key> --no-schedule`, so it runs only when someone asks. A snapshot workflow always has a schedule: pause it.
3. **Their own, or shared.** They change only their own, in their own folder (`connection.folder` of `kater_start`), `connections/<connection>/users/<email>/ai_tools/workflows/`. A shared workflow, in the connection's `ai_tools/workflows/`, is the organisation's template that nobody runs: to schedule it, make one of their own with `kater new workflow "<Name>" --start brief|general --cron <when> --timezone <zone> --file -`, whose steps follow the shared file ("Follow `connections/<connection>/ai_tools/workflows/<name>.md`, with these changes: …"). A Monday brief starts from [Monday Brief](vfs://guides/monday-brief.md), whose own file a person copies. Or ask the data team to change the shared one for everyone. To start from a workflow that does not follow the original's later changes, or from one that another person shared with them, `kater workflow duplicate <key> --label "<name>"` copies it into their own folder. The copy starts paused, so change it, then `kater workflow resume <key>`.
4. **Show the change and ask once:** its name, its steps and when it runs, in words. After a yes, run the `kater workflow` command.
5. **Check it.** `kater workflow show <key>` shows the schedule in words, who runs it and the next run, and `key_words` says which model key writes its words. When the organisation has no key for scheduled workflows, say that it still runs and asks them in their inbox for the words, and that an admin adds a key on AI settings. A watch needs no model key: its metric is checked with code. Then say when it runs next and where its result arrives: a snapshot's draft, a brief's page and answer, or a general workflow's answer in their inbox, a watch's row only when it crosses, and each run on the Workflows page.
6. **Run it now, when they want to see it work.** `kater workflow run <key>` starts a run and prints its id; `kater runs show <run>` reads it. It does not take the place of the next scheduled run.

## In their own agent, only when they ask

When the person asks that their own agent's scheduler run the workflow in place of its schedule in Kater, such as to keep it on their own computer, save `kater workflow save <key> --runs-in agent`. The schedule is then kept, but the workflow does not run in Kater. The task runs `prompt` from `kater workflow show <key>`, which only the owner gets, the same words as Copy prompt on the workflow's page. Give it to the scheduler as it is: it names the workflow's file, so the agent reads the steps again at each run.

- **You have a tool that creates or changes scheduled tasks,** such as `/schedule` in Cowork: after their yes, set up or change the task with it.
- **You don't, or the task is in another agent:** give them the prompt and the one setting to change there. Claude Cowork: **Scheduled**, the task, **Frequency** (hourly, daily, weekly or weekdays, no cron). Codex: **Automations**, the automation's custom schedule (cron). Cursor: cursor.com/automations, the **Scheduled** trigger's **Custom (cron)**.

Never say a task is set up, changed or stopped until it is. Then each change of its schedule or name needs the task changed too. `kater workflow save <key> --runs-in kater` gives the runs back to Kater: tell them to delete the task, so the workflow does not run twice.

## The shape

`kater new workflow` writes the frontmatter from its options and the steps from `--file`, a Markdown file, or `--file -` to read them from stdin. A general workflow's file looks like this, and a brief's has `kind: brief` too. A watch's file has `kind: watch` and a `watch` block, which `--start watch` writes with its steps ([workflows](vfs://guides/workflows.md#what-a-workflow-is)).

````markdown
---
label: Paid media Monday
name: paid_media_monday
description: Every Monday, brief me on paid media spend and return by platform for the last full week.
schedule:
  cron: "0 8 * * 1"
  timezone: America/Los_Angeles
---

# Paid media Monday

One sentence: what each run gives the person.

## Procedure

1. Each step in one or two plain sentences. Name each app, project, workspace or query it works with as a mention, `@[Name](<kind>:<ref>)`, with its kind first and an app or a project by its folder from the content root, such as `@[Paid media deck](app:connections/<connection>/users/<email>/apps/paid_media_deck)`. Write each mention outside backticks: a mention in a code span or a code block is only text. Name a field by its full path, and the skill or workflow it follows, such as vfs://guides/brief-me.md for Kater's brief me.
2. Never ask: take every input from the person's about.md or the defaults written here.
3. Report what it found as the run's answer. Its first sentence is the headline of the owner's inbox row, and the next two points follow it there.
````

A scheduled run follows the rules every answer does (the company's `rules.md`), with nobody there to answer: it writes only to the brainstorm folder and new queries in the person's own workspaces, and changes nothing shared. Nobody looks at the person's tabs during a run, so it never opens or moves one: it gives each page as a link in its answer.

## Stop conditions

- The workflow is someone else's: only its owner changes it. Offer a copy of their own, as step 3 says.
- The schedule runs more than once an hour, such as every 30 minutes: offer the nearest hourly one.
- With `runs_in: agent`, the Kater tools aren't connected to the agent that runs the task, so it can't read the workflow at each run: say so, and point to the steps that connect it, or offer to run it in Kater (`runs_in: kater`).
