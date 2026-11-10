---
name: projects-make
description: "Projects: read, make and change a project. Use it when the person asks for: \"Follow this goal over time\", apps grouped around a goal, with the metrics that measure it and one timeline."
---

This skill is Kater's guide `vfs://guides/projects-make.md`.

# Projects: read, make and change a project

A part of [Projects](vfs://guides/projects.md).

## Read a project

- `kater project list` lists the projects the person can read. `--app <folder>` keeps those that list one app, or a chart of it. `--can edit` keeps those the person may change.
- `kater project show <folder>` gives the goal, the time frame, the metrics, the people, the apps and what is open, in one answer.
- `kater timeline read <folder>` gives the timeline's 10 newest rows, each entry with its replies. `<folder>` is the project's or the app's folder from the content root, such as `connections/<connection>/users/<email>/projects/<name>`. Each `kater project` and `kater timeline` command that names a project or an app takes its folder, its key, `<connection>/users/<user_id>/<name>` as `kater project list` gives it, or its id (`rel1_…`). When an app and a project of one name have the same key, `kater timeline` refuses the key and names both: give the folder or the id of the one you mean. Each command that names an entry takes the entry's path: its file's path from the content root without `.md`, such as `connections/<connection>/users/<email>/projects/<name>/timeline/<name>`. Beside the project's or the app's folder, `--on` of `kater timeline read` and of `kater timeline log` also takes the entry's name alone. Run the command in its `older_rows` for the next page. `--brief` gives each entry's id, title, first words, author and state, its `reply_count` and its `newest_reply` (its day and title), so you can find the entry you need, and `--entries decisions` keeps only the decisions. A brief read gives each entry of the timeline you read by its name, as its id, and that timeline's folder once, as `target.folder`: so `kater timeline read <folder> --on <id>` and `kater timeline log <folder> --on <id>` take the id as it is, and the entry's path, which `kater timeline edit`, `remove`, `attach` and `detach` take, is `<target.folder>/timeline/<id>`. An entry of an app that a project's brief read holds has its path as its id. When you need only the decisions, such as to reply on the ones a run confirms or refutes, run `kater timeline read <folder> --brief --entries decisions`, never the whole timeline, and give it no `--limit`. A brief read gives as many decisions as fit one tool answer, about 30, so one call gives every decision of most projects. When the answer has `older_rows`, run that command for the next page. `kater timeline read <folder> --on <entry>` gives one entry with every reply. Read an entry's whole text in its file, at its `path`. Take figures from the timeline, and name the entry each figure comes from.

Read the project before you answer a question about it. For "What changed in Paid media budget since last week?", run `kater project show <folder>` and `kater timeline read <folder>`, then answer from them:

- Say what the newest snapshot found, with its figures, and name the snapshot by its day.
- Name each open decision (`open` in `kater project show`) and what it waits for.
- Name the entries since last week: who wrote what, and on which app.

Each figure on a timeline is a `<kater-value>` tag with `answer=` and `row=`: the rows it was read from, kept when the entry was written. Read those rows with `kater answer <answer id>` when you need more than the tag shows. Give every id at once, `kater answer <id> <id> <id>`, to read them in one call. A figure from a timeline is the figure of its day. When the person asks for today's number, run the query.

## Make a project

The person describes a goal, often from an app, in Kater's New project. Do this, in this order:

1. Read each app's queries and their weeks with `kater run`, so each number you propose is one you read.
2. Propose the metrics that measure the goal. For each metric, give the query, the field, the row and which way is better, and its last value with its normal range. Say which metric is the goal's.
3. Propose an end date when the goal has one, such as the end of a test or a quarter.
4. Tell the person what else the project should hold, and why.
5. Show the whole project as `kater new project` will make it: the title, the goal, the dates, the apps and the metrics.
6. Make it only after the person says yes, with `kater_apply`:

```bash
kater new project paid_social_conversion \
  --title "Paid Social conversion" \
  --goal "Bring Paid Social conversion back above 2% without raising its spend." \
  --chart "<app folder>:<workspace path>/<query name>" \
  --workspace <workspace path> \
  --starts 2026-04-06 --ends 2026-06-28 \
  --metric "<workspace path>/<query>.conversion_rate:Paid Social=Paid Social conversion" \
  --metric "<workspace path>/<query>.sum__visits:Paid Social" \
  --asked "Why did Paid Social drop?" \
  --why "Kater found that Paid Social conversion fell from 2.4% to 1.6% in two weeks, while its visits stayed the same. This project follows Paid Social conversion until it is back above 2%, or until Jun 28."
kater timeline log <project folder> --kind note --title "Paid Social conversion fell after the new landing page" \
  --capture "/app/<app key>/viewing?tab=<page>&slide=<slide>&select=<query name>:<from>..<to>" \
  --link "/workspace/<workspace path>?view=dashboard" --link-title "Paid Social conversion dashboard" --file -
```

NAME is the project's title in words or its folder name. Give `--title` with the title as the page shows it, such as "Paid Social conversion": left out, the title is NAME in words, "Paid social conversion". The folder takes its name from the title, `paid_social_conversion`, and a new title with `kater project set --title` moves the folder in the same change. Every file that names the project names it by its folder, and Kater rewrites them in the same change. Give `--app` once for each app and `--metric` once for each metric, the goal's first.

When the person starts from a type in New, give it with `--start` and its choices, and Kater writes the goal from them:

- `--start target --metric <metric> --target-value 3.5 --ends 2026-11-30`: "Get <metric> to 3.5 by Nov 30". The target goes on the first metric. A range is `--target-value "2.0 to 2.4"`: "Get <metric> between 2 and 2.4 by Nov 30". Leave `--target-value` out when the person has no number: the target is then the best fit, which Kater reads from the metric's normal range each week, its top when higher is better and its bottom when lower is. Do not make up a number for them.
- `--start test --decision "Move $2,000 a week from Meta to TikTok" --metric <metric> --readout-weeks 4`: the readout is the end date.
- `--start launch --goal "The spring sale" --starts 2026-11-01 --metric <metric>`.

`--source <path>` adds a dashboard, a query or an app the project follows, once for each. `--attach`, `--link` and `--capture` give the project files, links and pictures that it keeps as context, as an entry's are (see [Attach pictures, links and files](vfs://guides/projects-timeline.md#attach-pictures-links-and-files)). `--attach` takes an upload's id, `att_…`, or a file's content path.

The answer gives the project's `id`, `url`, its page from the portal's root (`/projects/<project key>`), and `link`, the whole link to give the person. It needs no `kater place`.

The project's first timeline row tells everyone why the project starts, so write it for a reader who did not see the conversation:

- `--asked` is the question the project starts from, in the words of the person who asked it. The row shows it first: "Sam asked: “Why did Paid Social drop?”".
- `--why` follows, in two or three short sentences: "Kater found … . This project follows … until … ." Say what you found, with the key figures and their weeks, then what the project follows and until when. Do not repeat the question, the goal or the metrics: the row lists them after it.

A metric is named from its field's label in its query, after its row key, such as "Paid Social Visits". To give it a name of your own, add `=<name>`, as the first `--metric` above does.

The first row holds no attachment. When the project starts from a chart, log a note right after `kater new project`, as above, with the figures behind what you found and a picture of the chart as the person sees it, with `--capture` (see [Attach pictures, links and files](vfs://guides/projects-timeline.md#attach-pictures-links-and-files)).

Kater reads each metric's weeks when the project is made, so its tiles and the goal's chart fill soon after, and then again each week. Until that read ends, the page shows that they load.

List a whole app with `--app` when the goal is about all of it, such as a weekly deck the team reads: the project then shows the app's snapshots and its timeline. List only one chart with `--chart APP:QUERY` when the goal is about one figure of an app, such as a question about one chart: the app's folder from the content root, a colon, and the query its page draws there, such as `--chart "<app folder>:<workspace path>/<query name>"`. A chart brings only itself: the app's timeline, snapshots and sharing stay with the app. `kater place` on the app's page lists the queries its pages draw. Give `--workspace` and `--query` for a workspace or a query of the person's own.

A project made in Kater's Manual form has no metrics. The next snapshot of one of its documents proposes them.

## Share a project

Share on the project page changes who can read the project's folder. Each person who can read the project also reads, as a Viewer:

- each app the project lists that the project's owner owns,
- each such app's snapshot workflow,
- each workflow that mentions the project.

Reading passes on, and running the queries of those apps. Nobody can change one of these because they can read the project. An app that someone else owns keeps its own access: only its owner shares it, from the app. Each person who can read an app also reads its snapshot workflow, and reads and runs each query its pages read in its owner's own folder, so its charts and figures show for them. A query in another person's folder keeps its own access, and the app's check before sharing names it. `kater project shares <folder>` lists what the project shares and each app it does not share, with each one's owner. Tell the person this before they share a project, so nothing is read that they did not intend.

## Change a project's apps

`kater project add <project> <app>` and `kater project remove <project> <app>` each write one commit. `--chart APP:QUERY` in place of the app adds or removes one chart of an app. The app, its timeline and its snapshots stay where they are. Show the change and run it with `kater_apply` only after the person says yes. Only the owner and the people who can edit the project's folder can change it.

## Change a project's settings and metrics

The person often asks in the project's settings, with Talk it through with AI: "Look at the goal and the metrics of my project ... Tell me what to add, change or remove, and why." Read the project with `kater project show <folder>` and each metric's weeks with `kater project metric read <folder> <name>`, which prints each week's value from the newest snapshot that read it, as the project page's chart draws them. With no name it reads the goal's metric. Then say, for each metric, what you would keep, change or remove, and why, citing its last value and its normal range. Say what else to track.

Make each change only after the person says yes, with `kater_apply`, one command for each change:

```bash
kater project metric set connections/<connection>/users/<email>/projects/paid_social_conversion paid_social_conversion \
  --alert below:0.015 --reach 0.02:2026-06-28 \
  --why "Paid Social conversion fell 3 weeks in a row. Sam wants to know before it drops below 1.5%."
kater project metric remove connections/<connection>/users/<email>/projects/paid_social_conversion paid_spend \
  --why "Spend is held flat by the budget, so it does not measure the goal."
kater project metric add connections/<connection>/users/<email>/projects/paid_social_conversion \
  "<workspace path>/conversion_by_channel" --figure "conversion_rate:Email" --label "Conversion rate" \
  --better higher --alert below:0.015 \
  --why "Email takes the visits Paid Social loses, so its conversion shows whether the move pays."
kater project set connections/<connection>/users/<email>/projects/paid_social_conversion --ends 2026-07-12 --why "The test runs 2 weeks longer."
```

- `kater project metric set <project> <name>` changes one metric: `--label`, `--better higher|lower|none`, `--alert below:<value>|above:<value>|change:<percent>|none`, `--reach <value>:<day>|none` (the target) and `--goal`, which makes it the goal's metric. What you leave out stays as it is. `kater project show` lists each metric's name.
- `kater project metric add <project> <query> --figure <field>[:<row key>]` adds figures of one query, one `--figure` for each. `--label` is the field as people read it, which each figure's label puts after its row key, such as Email Conversion rate. Recommend `--better` and `--alert` from the goal and the figures' weeks, as Add to does in Kater; left out, a figure has `better: none` and alerts outside its normal range. `--reach` gives each figure a target, which the query's charts draw as a line, and `--goal` makes the first figure the goal's. `--every` adds every figure of the query, and Kater adds each new row of it as it arrives. The first figure of a project is the goal's, and a figure the project holds already writes nothing.
- `kater project metric remove <project> <name>` takes one metric off. The goal's metric stays while there are others: make another one the goal first.
- `kater project set <project>` changes `--title`, `--goal`, `--starts`, `--ends` (`none` takes a date off), `--summary-from <app>` or `--tags`. Each `--tags` is `key=value` or a bare key, and together they replace the project's tags. `--tags none` takes them all off.

`--why` is the reason the timeline shows on the settings row, with what changed. Write it for the people on the project. Each command writes one commit, and the row names it as the person's agent, such as "Sally's agent changed the project's settings". A new metric also comes from the next snapshot's proposal, from the person's own settings, or from Add to on an app's card, a dashboard tile or the workspace editor's chart, which keeps the answer each figure came from as its baseline. A figure whose query no app or listed dashboard of the project draws still gets a week each week: Kater reads the query once a week for the project.
