---
name: daily-brief
description: "The Daily brief. Use it when the person asks for: What is in the person's brief, what stands out today, the projects they follow."
---

This skill is Kater's guide `vfs://guides/daily-brief.md`.

# The Daily brief

The Daily brief is Kater's own brief for one person: the things that stand out today in everything they can read, the projects they follow, what waits for them, what their alerts said, and the figures they keep. Kater writes it in code from a ranking it keeps each night. No model writes it, and no file holds it. Each morning at the person's time, it opens a row in their inbox, and a Slack DM or an email of it when they choose.

## Read it

Run `kater brief` when the person asks what is in their brief, for their Daily brief, or what stands out today. It reads the brief, the projects they follow and what waits for them at the same time, and answers in one call. Answer from it alone, in the order it gives:

1. **The one thing** (`one_thing`): its title, its value and change, its sentence, and its facts. Say why it stands out with `unusual` when it is there.
2. **What moved** (`moved`): each card in one line.
3. **My projects** (`projects`): each project in one line with its state, such as "Its test ended and it has no readout".
4. **Needs me** (`needs_me`): the count of each kind and its newest title.
5. **Alerts** (`alerts`): each figure whose alert goes to the brief and that spoke in the last day. It is there only when it has rows.
6. **Pinned** (`figures`): the figures they pinned, the ones that stand out first.
7. **Recommended** (`recommended`): the figures Kater selects for them that they did not pin. It is there only while Kater also selects figures.

The brief and For you give a person a figure that an app shows when they can read that app and its workspace. A figure that no app shows comes from its workspace's data, so it reaches only a person with execute access on that workspace, as a run does.

`stand_out` counts the one thing and What moved. Every value is Kater's, from stored rows: cite it as it is, and do not run a query to check it. A section the person turned off is not in the answer. When `ranking` is in the answer, Kater ranks the figures again now: say so, and give the rest. With `set_up` false, the person has no brief yet: say what Kater would select for it, from `kater_selects` and `projects_to_follow`, and that they start it on the brief's page, `/brief`, which `kater open /brief` opens.

To talk a card through, show where it points with `kater open --new` and its `open` address, or read it as [diagnose](vfs://guides/diagnose.md) says. A figure's `field` and `workspace` name where it comes from.

## Change it

The person changes their brief on its page in Kater, with Edit brief: its figures, its projects, its sections, its time and days, and Slack or email.

- **Pinned**: the person's pins, the same list as Pin on a chart. Add a figure with `kater pin add QUERY --figure FIELD[:ROW]`, and take one out with `kater pin remove`. Its value comes from what Kater stored.
- **Projects I follow**: the brief's projects, in their `about.md`, so their agent reads the same ones. Each item is the project's title, then its folder from the content root in backticks, such as ``- TikTok budget test (`connections/<connection>/users/<email>/projects/tiktok_budget_test`)``.

Record a project the person asks to follow under Projects I follow, as the about_me skill says. The brief reads it at its next read.
