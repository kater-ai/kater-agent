---
name: projects-timeline
description: "Projects: the timeline. Use it when the person asks for: A decision, a note or a question about an app or a project, to keep with its work; \"Add this conversation to the project\", a chat, thread or session for the people on a project to read."
---

This skill is Kater's guide `vfs://guides/projects-timeline.md`.

# Projects: the timeline

A part of [Projects](vfs://guides/projects.md).

## Add an entry

An entry is a decision, a change, an event, a note or a question on a project's timeline or an app's. Draft the entry from what the person said, show it to them, and add it with `kater timeline log <project or app folder> --kind <kind> --file -`, with the project's or the app's folder from the content root, its key or its id, only after they say yes. The entry's file is named from its title, such as `timeline/paid_search_rose_with_the_new_campaign.md`, with `_2` when the name is taken. A snapshot, a readout and a missed run take their day, such as `snapshot_2026_10_16`. The day of an entry is its `at:`, never its file's name. An entry is named by its path from the content root, with or without `.md`, or by its id (`rel1_…`). Write each figure as a `<kater-value query=... field=...>` tag, never as a typed number. Never change a snapshot: a later fact is a new entry.

Close what the entry answers. Give `--closes <item id>` once for each inbox item it covers. An entry `--on` an entry or a takeaway closes the items about it with no `--closes`.

## Attach pictures, links and files

An entry holds up to 10 attachments: a picture of a Kater page with a link to it, a link, or a copy of a file. Give them to `kater timeline log` with the entry, or add them to an entry the person wrote with `kater timeline attach <entry path>`. Show them with the entry, and add them only after the person says yes. Each option can be given more than once.

- `--capture "<address>"` attaches a picture of a Kater page and a link to it. Give the address as the person sees the page: an app's slide with the range they selected, such as `/app/<app key>/viewing?tab=<page>&slide=<slide>&select=<query name>:<from>..<to>`, or a workspace's dashboard, `/workspace/<workspace path>?view=dashboard`. Take it from the prompt, or from `kater here`. The picture shows the page as the person sees it, and it shows on the timeline within about a minute. `--capture-title` names it; left out, it gets a name such as "Slide 3 of <app>".
- `--link <address>` attaches an `https:` address, or a Kater page's address that starts with `/`. `--link-title` names it; left out, the Kater object's name or the address.
- `--attach <file>` keeps a copy of a file: a file on the person's computer, by its full path, or a file they can read in Kater, by its content path, such as a CSV in their `brainstorm/` folder. A file on their computer can be read only in their own terminal and through a Kater MCP that runs on their computer. A file is 10 MB at most: PNG, JPEG, WebP, GIF, PDF, CSV, JSON, XLSX, DOCX, PPTX, text or Markdown.

Never describe or attach a picture that the person pasted into the conversation: attach the Kater page it shows with `--capture`. `kater timeline read <folder> --json` lists each entry's attachments with their ids and states. `kater timeline detach <entry path> <attachment id>` takes one off. When a picture failed, `kater timeline attach <entry path> --capture "<address>" --replace <attachment id>` takes it again. Only the person who wrote an entry adds attachments to it, and a snapshot, a readout or a missed run takes none.

## Add a conversation to a project

The person asks to add this conversation, chat, thread or session to a project, so that the people on the project can read it. No conversation is kept in Kater. The entry holds the link that the person's own tool makes when they share the conversation with their organisation, or a copy of the file that their tool exports. Never write the conversation out yourself in its place: no transcript, no summary of each turn and no chat thread app. An entry on a project needs edit access: when the person does not name the project, `kater project list --can edit` lists the projects they can add to.

1. **Ask the person for the link.** You cannot make a share link in any tool. Only the person can, in their tool's own screen. Never make the export yourself, such as from a tool's own session files on the disk: wait for the person's link or file. Tell them how, in one or two sentences for the tool that you run in:
   - A chat on claude.ai or in the Claude app: select Share at the top of the chat, then copy the link. On a Team or Enterprise plan, only people of their organisation can open it. The link shows the messages up to that moment, and Update shared chat adds the later ones.
   - Claude Code on the web: set the session's visibility to Team, then copy its link. A reader sees the session as it is when they open it.
   - Claude Code in a terminal, in VS Code or in the desktop app: there is no share link. `/export <file name>` writes the conversation to a text file. Go to step 3.
   - A different tool, such as Codex: share the conversation as the tool lets you, then give me the link or the exported file.
2. **With a link, log a note.** Write the note from what the person says that the conversation found or decided, in their words, in two or three sentences, with each figure as a `<kater-value>` tag. Attach the link with `--link`, and name the conversation with `--link-title`. Show the entry, and add it with `kater_apply` only after the person says yes. Tell them that the link opens only for the people that their tool lets in. The person's own folder is `connection.folder` of `kater_start`.

```bash
kater timeline log connections/<connection>/users/<email>/projects/paid_media_budget --kind note --title "Why Paid Social conversion fell" \
  --link "https://claude.ai/share/<id>" --link-title "Claude chat: why Paid Social conversion fell" --file -
```

3. **With an exported file, attach a copy of it.** Read the whole file first. When it holds a secret, such as a token, an API key, a password, a private key or a connection string with a password, do not attach it: tell the person what you found and where, and ask them to remove it and export again. When it holds data, such as figures from a query, a dashboard or an export, tell the person in one sentence what data it holds and that the project's readers will see it: sharing it is their choice. Show the entry with the file's name, and add it with `kater_apply` only after the person says yes, with the file's full path in `--attach`. A text or Markdown file can be 10 MB at most.

```bash
kater timeline log connections/<connection>/users/<email>/projects/paid_media_budget --kind note --title "Why Paid Social conversion fell" \
  --attach /Users/<you>/paid-social-session.txt --file -
```

A Kater MCP that does not run on the person's computer cannot read the file, and answers that there is no file at that path. Then put the file, unchanged, in their brainstorm folder first, with `kater source write connections/<connection>/users/<email>/brainstorm/paid-social-session.txt --text-file -` and the file's whole text in `stdin`, and attach that content path.

## Log a decision

A decision's settings go in `--decision-file`, as JSON. Each figure is `{query, field, row}`: the query's workspace path and name, the field, and `row`, a list of the row's key values, such as `["Paid Search"]` for one channel's row. An empty list, `[]`, is the sum of the week's rows. `row` is always a list, never one text.

```bash
kater timeline log connections/<connection>/users/<email>/projects/paid_media_budget --kind decision \
  --title "Move $5,000 a week from Paid Social to Paid Search" \
  --decision-file decision.json --closes inb_1be9d35feab79abc00f6e1a71f838186 --file -
```

```json
{
  "start_when": {"figure": {"query": "<workspace path>/channel_spend_daily", "field": "daily_spend", "row": ["Paid Search"]}, "daily_spend": 1500},
  "weeks": 4,
  "readout": {"app": "connections/<connection>/users/<email>/apps/paid_media_spend_and_return", "on": "2026-05-18"},
  "guardrail": {"figure": {"query": "<workspace path>/weekly_scorecard", "field": "week_conversion_rate", "row": []}, "at_least": 0.018},
  "watches": [{"query": "<workspace path>/weekly_scorecard", "field": "week_spend", "row": []}]
}
```

`start_when` names the row of a daily query that holds the platform's spend a day, and Kater's nightly job marks the decision started on the first day that row reads within 5% of `daily_spend`. It then pins the platform's figures for the `weeks` before the start as the baseline, and moves the readout's day by the days the start came late. When the decision has not started 7 days after it was logged, its owner gets an item. A decision with no `start_when` starts at its own `at`. `readout` names the snapshot that reads the result out: the app, by its folder from the content root, and the day.
