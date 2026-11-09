---
name: projects
description: "Projects. Use it when the person asks for: \"Follow this goal over time\", apps grouped around a goal, with the metrics that measure it and one timeline; What waits for the person: a draft to review, a takeaway to act on, a question to answer."
---

This skill is Kater's guide `vfs://guides/projects.md`.

# Projects

A project is a goal, the metrics that measure it, a list of apps and one timeline. A person makes one to keep the story of some apps in one place: what each run of a document said, what the team decided, and what happened. An app stays in its own folder, and it can be in several projects.

## What a project holds

Kater keeps a project in one file, `projects/<name>/kater.project.yaml`, in its owner's folder. A file or a mention names a project by its folder from the content root, such as `connections/<connection>/users/<email>/projects/<name>`. A `kater project` or `kater timeline` command and a portal address take its key, `<connection>/users/<user_id>/<name>`, or its id (`rel1_…`), and a command takes its folder too. `kater project list` gives each project's folder, key and id. Never write the file yourself. Each change is one commit that a `kater project` command makes.

- **Title.** A short name, such as `Paid media budget`.
- **Goal.** One sentence that says what success is, such as "Grow attributed revenue from paid media without raising total spend." A goal is not a question.
- **Starts and Ends.** Both are optional days, such as `2026-04-06`. With no end, the project runs until its owner closes it.
- **Tags.** Optional. Each key has a list of its values, such as `topic: [paid_media]`, as a workspace's, a query's and an app's tags are written. A search finds the project by them.
- **Apps.** The apps it groups, each by its folder from the content root, such as `connections/<connection>/users/<email>/apps/retention_scorecard`.
- **Charts.** One chart of an app in place of the whole app: the app's folder and the query its page draws there. The project page shows it with the app's title for it, and opens the app at it.
- **Metrics.** The numbers the project is judged by. Each is one query, one field and one row a week, or the sum of the week's rows. One metric is the goal's. Kater computes each metric's normal range and status from its weeks, so nobody has to set a level. A metric's target draws as a line on each chart of its query, and its owner hears about its alert in their Inbox: see [pin and alerts](vfs://guides/pin-and-alert.md).

The timeline of a project holds its own entries and the entries of each app it lists that the reader can read, newest first, in weeks.

In Markdown you write, such as a timeline entry, a project's goal or a workflow's steps, name each Kater object with a mention, `@[Name](<kind>:<ref>)`, written as [a workflow's steps](vfs://guides/workflows.md#what-a-workflow-is) say: its kind is `app`, `project`, `workspace` or `query`, and a query's ref is `<workspace>/<name>`, never its file. To find what a mention names, use its kind and its ref, never its name. A mention in a code span or a code block is only text.

## The words

- **Document.** An app that a snapshot workflow writes, on a schedule, such as a weekly deck, or one time, such as a deck whose numbers stay as they were on the day it was made. Each run of it is a snapshot. Workflows, their four kinds (snapshot, brief, watch and general) and the `kater workflow` commands are in vfs://guides/workflows.md.
- **Tool.** An app a person works in, such as a budget planner. It has no snapshots. Its entries say what someone did with it.
- **Snapshot.** One run of a document: the page as it was, its figures pinned to the rows they came from, and its words, a few takeaways and a sentence or two for each part of the page. The workflow's owner keeps it, and then it is an entry on the timeline. Nobody changes it after that, and it is view-only in the web app. A dashboard has a snapshot each week too: the board as it drew that week, with its charts, looks, labels, numbers and theme. It stays the same whatever happens to the live dashboard, its charts, its queries or its fields afterwards, and only the live dashboard shows a broken chart, so fix the live dashboard and never a snapshot.
- **Entry.** One file on a timeline: a snapshot, a readout, a decision, a change, an event, a note or a question. A reply is an entry `on` another one. Only the person who wrote a note, a question or a reply can change its words, with `kater timeline edit <entry path> --file -`, or remove it, with `kater timeline remove <entry path>`. Show the change, and run it with `kater_apply` only after they say yes. A removed entry stays in the history, and its replies stay on the timeline. Every other entry stays as it was written: to correct a decision, a snapshot or someone else's entry, add an entry `on` it that says what changed.
- **Decision.** An entry that says what the team will do. It can say when it starts, how many weeks it runs, which snapshot reads it out, a guardrail that must hold and the figures it watches.
- **Inbox item.** Something that waits for one person: a draft to review, a takeaway to act on, a question about their app. It closes when an entry answers it, or when the person marks it done. A standing row, such as the references that need a fix, closes by itself when its count reaches 0. Any other item never closes by itself.

## The steps

Read the part for what you do:

- **Read, make, share or change a project:** [read, make and change a project](vfs://guides/projects-make.md).
- **Add an entry, a picture, a link, a file, a conversation or a decision to the timeline:** [the timeline](vfs://guides/projects-timeline.md).
- **Make a document, write a snapshot's words, or propose metrics for a goal:** [documents, snapshots and proposed metrics](vfs://guides/projects-documents.md).
- **Talk through what waits for the person:** "The inbox" below.

## The inbox

Run `kater inbox` when the person asks what waits for them, or to see whether something waiting is about what they ask. It lists what waits for the person, newest first, each with its reason, its title and the command that reads its place. The inbox is the one place Kater tells a person something, so it also lists a promotion request to review and the objects no one manages, each with its own words and no place to read when it is about Kater itself. When they asked what waits, say it: "Two things wait for you: ...", then ask which one to talk through. Otherwise raise only an item about what they ask, such as a draft snapshot of the app their question is about.

`kater inbox show <id>` gives one item: its messages, the prompt for it, and its project's open decisions and newest entries.

Talk an item through like this:

1. Read its place first, with the `read` command the item gives.
2. Say what the figures show, citing each one from the timeline or a query you ran.
3. Lay out two or three options, each with the figure it should change.
4. Recommend one, and say why.
5. Show the entry you would add, with its `--closes`.
6. Ask. Add it with `kater_apply` only after the person says yes.

For a move that nothing on the timeline explains, list the likely causes, such as a campaign that started, a budget change or a tracking change, and ask which one it is. Do not pick one.

When the person says an item needs nothing, ask, and then run `kater inbox done <id>` with `kater_apply`. When an entry answers it, use `--closes` instead, so the item says what answered it.
