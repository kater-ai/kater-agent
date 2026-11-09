---
name: chart-question
description: "A question about a chart. Use it when the person asks for: A question about a chart, or a part of it the person selected, from a prompt that Ask AI copied."
---

This skill is Kater's guide `vfs://guides/chart-question.md`.

# A question about a chart

Ask AI on a chart copies a prompt that the person pastes into a conversation, with a question of their own or the default "What happened in the part of this chart I selected, and why?". The prompt names the chart (its title, its kind, the app, the page and the slide that draw it, its query by its workspace path and name, and `kater answer <id>` for its rows), the selection (the first and last period, the series that are on, and the selected rows, which are the person's view) and where the person is (the page's address and the live tab). So do not run `kater here`, `kater place`, a catalog search or a list of files to find these.

A question about why the chart moved, or what happened in the selected periods, is a diagnostic question: follow [Why a figure changed](vfs://guides/diagnose.md), with what this guide adds. Any other question, such as how a figure is computed or what the chart shows, gets its answer directly.

## What the chart adds

- **Restate from the prompt.** The selected rows are what the person sees. Restate the change from them before any call.
- **The chart's query is how the figure is computed.** Read the file that declares it and `vfs://<workspace>/workspace.sql`: the shared model it reads, each `REQUIRED_FILTER`, each series' value as the rows hold it, and the labels and formats the chart shows. The prompt names the query as `<workspace>/<name>`; `kater query list <workspace>` gives its file (`path`), which can hold several queries and is not named after them. Use the same labels and formats in your statements, so your charts read as the person's chart does.
- **Read `kater answer <id>`** only when the prompt says its rows are cut short.
- **What people recorded about the chart is on its app:** `kater project list --app <app folder>` lists the projects that follow it, and `kater timeline read <app folder> --since <date>` and `kater history <app folder>` show its decisions, notes and changes.
- **A picture the person pastes** shows what they see. Take every figure from a result, never from the picture.

## Offers a chart allows

Along with the offers in [Why a figure changed](vfs://guides/diagnose.md#7-offer-what-helps-next), a chart in an app allows these. Offer only what fits what the person asked, one at a time and in any order, and say what each gives them. Change nothing else: not the chart's workspace, and not its app except a slide's notes.

- **A project that follows this chart.** `--chart "<app folder>:<chart's query path>"` on `kater new project` lists only this chart of the app ([Projects](vfs://guides/projects-make.md)). A note on the project can hold a picture of the slide as the person sees it, with their selection, by `--capture` with the page's address as the prompt names it.
- **Talking points on the slide,** when the chart is on a slide of a deck. `read_file` the page the prompt names and find the slide's `<aside class="slide-notes">`, right after its `<section class="slide">`. Tell the person what you add and ask first, since everyone who opens the deck sees it. On a yes, write the page again with `kater_apply`, `source write PATH --text-file - --expected-revision <its sha256 from the read>`, with that aside changed only: keep its notes, and add the points as `<p>` after them, before its `<kater-appendix for="slide" folded>`, which stays last. Write each figure as a tag on the chart's answer, never typed: `<kater-value query="<chart's query path>" field="<field>" answer="<answer id>" row="<row>"></kater-value>`, with `row` counted from 0 in the order `kater answer <id>` lists the rows.
- **A dashboard of what you found.** Add each charted statement to a new workspace of the person's own, with its chart on the dashboard, then lay out the board:

  ```bash
  kater add-to <file> --statement 0 --to new:dashboards --name "<workspace label>"
  kater add-to <file> --statement 1 --to workspace:<new workspace path>
  kater dashboard fill <new workspace path> --request-file -
  kater dashboard text add <new workspace path> --before <chart name> --file -
  kater compile --select <new workspace path>
  ```

  On a yes, run each line but `compile` with `kater_apply`. Each add takes about 30 seconds. The first answers the new workspace's path in its `Open it:` line. `dashboard fill` takes `{"charts": [{"chart": "<chart name>"}]}` on stdin, and sends the workspace its first argument names. Tell the story in text tiles as [A report on a fixed time window](vfs://guides/dashboard-file.md#a-report-on-a-fixed-time-window) says. In a local copy, `kater add-to` writes in Kater and brings the workspace into the copy.
