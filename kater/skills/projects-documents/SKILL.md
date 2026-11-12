---
name: projects-documents
description: "Projects: documents, snapshots and proposed metrics. Use it when the person asks for: What changed in an app this week, its takeaways and figures."
---

This skill is Kater's guide `vfs://guides/projects-documents.md`.

# Projects: documents, snapshots and proposed metrics

A part of [Projects](vfs://guides/projects.md).

## Make a document

A deck, a report or a digest is a document, even when the person wants it once. Make it with its type, so its snapshot workflow comes in the same commit, its words come from a run and Run again works from the first day:

- Ask the person if the document gets new numbers, or keeps the numbers of today. With new numbers, it runs on a schedule: ask when, such as every Monday at 8:00 AM. Make it only after they say yes, with `kater new app <name> --start deck --numbers live --cron "<cron>" --timezone <zone>`, or `--start report` (`--paper a4` for A4 sheets) or `--start digest`. With the numbers of today, give `--numbers static` and no `--cron`: the workflow has no schedule, and its one run now is the document. Add `--source <path>` for each dashboard, query or app it reads. The command writes the manifest, the first page, which names its template, and the workflow in the person's folder, with `writes` naming the app (vfs://guides/workflows.md). When its words name a period, add `period` to the workflow's frontmatter (`hour`, `day`, `week`, `month`, `quarter` or `year`), such as `period: month` for a monthly review: each snapshot then compares that period with the one before. Without it, a snapshot compares the period its schedule runs once per, else a week.
- Put each sentence that judges the numbers, such as "Email converts the most visits", in an empty `<kater-words name="…">` where it goes on the page, never as typed text. A name is snake_case, such as `spend`, and each name is on the page once. A slide's header, its footer and its notes hold no `<kater-words>`.
- The workflow's steps say how to write a snapshot, with the app as a mention. Keep the steps that `kater new app` writes, and add only what this document needs. The person's own folder is `connection.folder` of `kater_start`:

```markdown
1. Run `kater snapshot draft` on @[Retention Scorecard](app:connections/<connection>/users/<email>/apps/retention_scorecard) and do what it says next.
2. From the packet, write at most three takeaways and at most two sentences for each `<kater-words>` name. Cite every number by its figure id, such as `{f04}`, and list in `facts` each figure an item reads, at least one with `checks`.
3. Run `kater snapshot save` with the words. If it refuses them, fix what it names and save once more.
```

When the page is written, run the workflow once with `kater workflow run <key>`, after the person says yes, so the document has its first snapshot: the queries run, the words are written, and the draft waits in the person's inbox. For a document with no schedule, that snapshot is the one it keeps, and the person runs it again only to get new numbers. The person reads the draft and adds it to the timeline.

A deck or a report made before this rule has its words typed on the page and no workflow. Its owner sees "The words on this page are from …" while viewing it, and selects Set up a snapshot workflow, which opens New workflow in the app's Workflows tab: each block they select gets an empty `<kater-words>` in its place, and the workflow file is written with them in one commit. Do not convert one yourself.

A change to a document's page shows at Live and in the snapshots taken after it. An earlier snapshot keeps the page at its own commit, so its words and figures stay as they were.

## Write a snapshot's words

`kater snapshot draft <app>` gives a packet: each figure with its number, such as `f01`, its value, the period before and whether it stands out. The period is the workflow's `period`, a week when it names none. Write the words from the packet only:

- At most three takeaways, then a sentence or two for each part of the page (each `<kater-words>` name).
- Cite each figure by its number, `{f01}`, or `{f01.before}` for the period before. Never type a number.
- List in `facts` every figure an item reads. At least one of them has `checks`: it is a row of Compare, and the item's source. A reader selects the item's status to open that row with its two periods. Each item's rows are kept in the entry's `compare`.
- Lead each item with its status: `new`, `changed`, `still_true` or `resolved`. Any but `new` names in `follows` the item of the last snapshot it follows.
- Put in `entries` the ids of the entries an item names, such as an open decision.

Save them with `kater snapshot save <run> --file -`. A refused save says each problem: fix them and save again. Only the workflow's owner keeps or discards a draft, after they read it.

## Propose metrics for a goal

Read the apps' queries and their weeks with `kater run` first. Propose a metric only from a query that has one row a week, or whose week's rows add up. For each, give the field, the row, which way is better, and its last value. Make the goal's metric the one the goal names.
