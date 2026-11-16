---
name: follow
description: "Follow and markers: what a person does with a number they care about. Use it when the person asks for: \"Tell me when ROAS changes\", \"keep an eye on this\", \"follow this\", \"draw a line at 3\", \"show the average\": a chart's star or Add marker."
---

This skill is Kater's guide `vfs://guides/follow.md`.

# Follow and markers: what a person does with a number they care about

A chart offers three things, and each does one thing. Say what each does, and use the one the person means.

| What the person says | On the chart | What it does | The command |
| --- | --- | --- | --- |
| "Keep an eye on this", "tell me when ROAS changes", "follow this" | The star | Follows the chart or one of its numbers. They hear when it meaningfully changed. | `kater follow add` |
| "Draw a line at 3", "show the average", "shade the target zone", "mark the day we launched" | Add marker | Draws a line or a band, a note for the chart's readers. It tells nobody. | `kater chart marker add` |
| "Add this to the project" | Add to | Puts it in a project, a dashboard, an app or a workflow. | `kater add-to`, `kater project figure add` |

A number is one field at one row of a query, written `<field>[:<row key>]`, such as `platform_roas:TikTok Ads`. A query is its workspace's path and its name.

Each command sends what the page sends, so the person sees the result on the chart and on Home at once. A follow and a person's own marker are kept in Kater; a marker for everyone is in the chart's file ([Markers](#markers)).

## Markers

A marker is a note on the chart for its readers: a horizontal line at a value or at a statistic of a measure, a vertical line at a date, or a band between two values or two dates. The chart writes the marker's label first and its value, statistic or dates after it, such as `Target $7` or `Average $3.99`.

```text
kater chart marker list QUERY [--chart NAME]
kater chart marker add QUERY --chart NAME --value platform_roas=3 --label "Break even"
kater chart marker add QUERY --chart NAME --stat platform_roas=average --tone warning
kater chart marker add QUERY --chart NAME --value platform_roas=3.8 --to 4.2 --label "Target zone"
kater chart marker add QUERY --chart NAME --day 2026-10-05 --label "Launch"
kater chart marker add QUERY --chart NAME --day 2026-09-07 --to 2026-09-21 --label "Promo"
kater chart marker change QUERY MARKER --chart NAME --value platform_roas=3.5
kater chart marker remove QUERY MARKER --chart NAME
```

- **At a statistic.** `--stat <field>=<statistic>` draws a line at the measure's `average`, `median`, `highest` or `lowest` over the rows in view. It moves when the data or a filter changes. A marker sits at a value or at a statistic, never both.
- **A band.** `--to` is the far end of a band: a number after `--value`, a date after `--day`. It is after the start. A band has no statistic.
- **A tone.** `--tone` is `neutral`, `positive`, `warning` or `negative`, which the chart shows as Neutral, Good, Watch and Bad. It colours the line or the band and its words.

- **Who sees it.** A person who can edit the chart writes it in the chart's file, for everyone. Anyone else draws one only for themselves. `who` in the answer says which: `everyone`, `only_me` or `project`. Say it to the person.
- **The `kater` tool** writes a person's own marker at once. A marker for everyone commits to the chart's file, so `kater` refuses it, and the same line runs through `kater_apply` after the person says yes.
- **A project's target** is a line with `who: project`. It changes in the project, with `kater project figure set PROJECT NAME --reach`.

The chart's file holds its markers under `chart:`, in `markers:`. Change them with these commands. A hand edit keeps the same shape, with at most 32 lines:

```yaml
chart:
  markers:
  - {id: mk_break_even, on: value, field: platform_roas, value: 3, label: Break even}
  - {id: mk_average, on: value, field: platform_roas, stat: average, tone: warning}
  - {id: mk_zone, on: value, field: platform_roas, value: 3.8, to: 4.2, label: Target zone}
  - {id: mk_launch, on: period, day: 2026-10-05, label: Launch}
  - {id: mk_promo, on: period, day: 2026-09-07, to: 2026-09-21, label: Promo}
```

A marker changes how the chart looks, so it never runs the warehouse.

## Follow

```text
kater follow list [--query QUERY]
kater follow add QUERY --chart NAME
kater follow add QUERY --figure "platform_roas:TikTok Ads" [--label ROAS]
kater follow change QUERY --figure "platform_roas:TikTok Ads" --low 3
kater follow remove QUERY --figure "platform_roas:TikTok Ads"
```

- **One word.** Following is one gesture, the star on a chart or `kater follow add`. The person invents no threshold: the number's usual range, worked out from its own periods, decides when it meaningfully changed. Never ask them for a number, and never write one for them.
- **A chart or one number.** `--chart NAME` follows the saved chart: every number it draws. `--figure FIELD[:ROW]` follows one number, once for each command. Give one of the two, never both. A number followed alone and its chart followed too are two follows.
- **What they may set.** `--low` and `--high` are a lowest and a highest, and `--percent` is how far the number may move from the period before. A percent stands alone, and a chart takes only a percent. `--better higher|lower|none` says which way is good news. `kater follow change` leaves out what stays, and `--usual-range` puts the usual range back. Set a bound only when the person says a number.
- **Where it tells them.** The Inbox always, with no switch. `--channel` adds `brief`, `slack` or `email`, once for each place, and `inbox` alone means the Inbox only. Left out, it is the brief. Given to `change`, it replaces the places the follow has. Slack needs the organisation's Slack and their own account connected, and the command says when it does not.
- **When it tells them.** The morning's job reads each followed query once and tells the person once on the morning a number crossed, also when the number is in their project too.
- **What the answer says.** `kater follow add` answers where the number stands in a sentence, such as `TikTok Ads spend was $18,400 last week, inside its usual $15,000 to $21,000.`, and where it tells them, such as `Monday's brief names it when it meaningfully changes.` When they ask you to keep an eye on a number, run it, then give them both sentences as the command words them. A number not yet read answers that it is being read: say so, and do not run a query to check it. A follow that existed answers so, with the settings it has; to change them run `kater follow change`.
- **From the goal Follow a number.** `kater start goal --shape follow --metric NAME` lists the person's followed measures and the dashboards and apps that read the metric. Find the saved chart that draws it, say which chart you will follow, and after a yes run `kater follow add QUERY --chart NAME`. Follow the chart, not a number of it, unless the person named one.
- **What `kater_start` tells you.** Its `following` is what the person follows: how many charts and numbers, and the numbers that moved first, each as the sentence to give them. Lead with these when they ask how things are going, name a number outside what they set first, and say nothing of a number they do not follow unless they ask. `memory` in the same answer says how the company's words and the person's Memory work together. `This person follows nothing yet.` says to ask what they want to follow.
- **What they know about themselves.** `kater memory` prints what the person's agents read: Following, Suggested, About you, Your company and Learned. Print it when they ask what you know about them, and say each part in that order. To forget, take out what they name and say what you took out and how to undo it: a follow with `kater follow remove`, whose answer gives the line that follows it again, and a line of their `about.md` as [about me](vfs://guides/about-me.md) says.
- **Which numbers people follow.** `kater follow top` is for a Developer or an Admin: a count for each number, and never who follows it: never try to find out who, with any command or file. A number that is new today and that no rule names comes with the question to answer, such as whether ROAS counts refunds. Offer to propose the rule with `kater propose rules.md`.
- **The limit.** A person follows at most 200 numbers and charts. The refusal says so and what to do.
- **Execute access.** A number is the query's data, so a follow needs execute access on the query, as a run does. Without it, the refusal says that the person can open the query, not run it, and `kater follow list` leaves out the follows of each query the person cannot run. Taking out a follow needs none.
- **The `kater` tool** runs these with no question, since a follow is the person's own. Follow only when they asked for it, and say what it will do. Unfollowing answers with the line that follows it again.

`kater follow list` reads what is stored and runs no query. Each row gives `says`, `status` and where it tells them. The person sees the same rows on Home, under Following.

## A follow with a line

"Tell me when ROAS drops below 3" is a follow with a lowest, with no workflow and no model key: `kater follow add QUERY --figure "platform_roas" --low 3 --better higher`. A highest is `--high`, and a number that may move a share from the period before is `--percent`. Set the line only when the person says one. Their words give the way: "drops below" is `--low`, "goes above" is `--high`. With no line, follow the number and the usual range decides. To move the line later, `kater follow change QUERY --figure "platform_roas" --low 2.5`.

A number that no saved chart draws is followed the same way, from a query of a workspace. When none answers the metric, find its verified definition as [brief me](vfs://guides/brief-me.md) does, draft the query, show the periods that were past the line (they are the times it would have told them), and after a yes save it with `kater add-to FILE --statement N --to workspace:WORKSPACE` before you follow it. Say what happens next: the morning's job reads it, and the Inbox tells them once on the morning it crossed.

A follow is read each night. A check at an hour of their own, which is rare, is a scheduled workflow ([workflows](vfs://guides/workflows.md)).

A figure in a project is checked too: a project's owner hears about each figure's alert in their Inbox with no follow of their own. See [projects](vfs://guides/projects.md).
