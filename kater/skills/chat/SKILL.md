---
name: chat
description: "Chat template. Use it when the person asks for: \"Document this conversation\", \"send the team what we found\", a record of questions and answers."
---

This skill is Kater's guide `vfs://guides/chat.md`.

## Chat template

A chat thread is a conversation with the data assistant written out as an app page, so someone who wasn't in it can read it and use it. It reads like Kater's own chat, with the same parts as the Ask panel: each question in the chat's bubble on the right, each answer as the assistant gave it, at the chat's text size, with the charts, tables and figures it showed. Every figure is a Kater tag, so the thread runs its queries again whenever it's opened, with the reader's own access. Above each answer the runtime writes one line for each query the answer read, drawn as the Ask panel draws a step. The line says the query is running, then that it ran and how many rows came back. Opened, it shows what ran. The page names the template on its root:

```html
<html lang="en" kater-template="chat">
```

Write one when the person asks to share, document, keep or send the conversation, for example "document this in a chat thread" or "make this something I can send the team". To add the conversation itself to a project, follow [Add a conversation to a project](vfs://guides/projects-timeline.md#add-a-conversation-to-a-project) instead: the entry links to the conversation as the person shares it from their own tool, and nobody writes it out. The template is a stylesheet and a small script the runtime serves, never a copy in the app folder. It uses only the theme's tokens and follows the host's light or dark mode.

## Skeleton

```html
<!doctype html>
<html lang="en" kater-template="chat">
<head>
  <meta charset="utf-8">
  <title>Paid media spend by platform</title>
</head>
<body>
  <main class="chat">
    <header class="chat-header">
      <h1 class="chat-title">Paid media spend by platform</h1>
    </header>

    <article class="chat-turn" data-role="user">
      <p>How has our paid media spend been trending over the last few months?</p>
    </article>

    <article class="chat-turn" data-role="assistant">
      <p>Spend was <kater-value query="connections/<c>/workspaces/<w>/spend_trend" field="spend" row="0"></kater-value> in <kater-value query="connections/<c>/workspaces/<w>/spend_trend" field="month" row="0"></kater-value>.</p>
      <kater-chart query="connections/<c>/workspaces/<w>/spend_trend" kind="line" dimension="month" measures="spend"></kater-chart>
    </article>

    <kater-appendix folded></kater-appendix>
  </main>
</body>
</html>
```

## Writing the thread from the conversation

- One `chat-turn` per message, in the order they were said. A `data-role="user"` turn holds the person's words as they wrote them. A `data-role="assistant"` turn holds the answer.
- Keep what each answer said and showed. Leave out narration of the work ("Let me check…", "I'll run…"): the runtime writes a line for each query, so the page never says which tool ran. Leave out turns that were only about the tools, and the request to make the thread itself.
- Every figure an answer showed is a tag naming the query that produced it: a chart as `<kater-chart>`, rows as `<kater-table>`, headline figures as `<kater-kpi>`, and a number inside a sentence as `<kater-value>`. Use the same kind and fields the answer showed. Give each card the title the answer gave it with `heading`; without one a card shows its query's title: its `label`, or Kater's words for its fields and date window when nobody wrote one.
- Never type a figure, a date or a period into the words. The thread runs again whenever it's opened, so typed numbers go stale while the tags stay current. Write words that hold when the figures move: "Spend was X in Y" with both as `<kater-value>`, rather than "spend fell 12% in August". State a direction or a ranking only when a query returns it, such as a change column or the first row of a query ordered by the figure it ranks.
- A tag names a saved query. An answer that came from `kater run` on a saved query names that query. An answer that came from a scratch statement needs a saved query first: add it to one of the person's own workspaces over the same source with `kater add-to FILE --statement N --to workspace:WORKSPACE`, as [the Sandbox guide](vfs://guides/sandbox-charts.md#adding-a-statement-to-dashboards) says, and name that query. Never point a tag at a query other than the one that produced the answer.
- Make a query the thread needs and no workspace has like any other query, by `connections/<connection>/ai_tools/skills/query_authoring.md`: `kater query fields WORKSPACE`, then `kater query new WORKSPACE --request-file -` in one of the person's own workspaces, then `kater model edit` for each change after that. Write SQL only when no pick makes the change, as the skill says.
- A tag runs its query at its parameters' defaults. When the conversation ran a query with other values, save a query whose defaults are those values, or add a `<kater-filter>` (below) whose starting values match.
- Keep each answer about what its question asked. A question that names a period, such as "the August drop", is answered for that period whenever the thread opens: its queries take the period as a parameter whose default is the one asked about, never "the latest month". A `<kater-filter>` on that parameter lets a reader ask the same question of another month. A question about "lately" or "the last few months" finds its period from the rows, so its answer stays current.
- End the thread with `<kater-appendix folded>` after its last turn: the sources list, folded under one Sources line ([Sources](vfs://guides/app-sources.md#sources)). Never write where a figure came from in an answer's words.
- Name the thread after what it found out or asked, in the person's words, in both `<title>` and `chat-title`, such as "Paid media spend by platform", never "Chat thread".

## Blocks

| Class or attribute | What it draws |
| --- | --- |
| `chat-header`, `chat-title`, `chat-subtitle` | The thread's title at its top, with a rule under it |
| `chat-turn` with `data-role="user"` | A question, in the chat's bubble on the right, as in the Ask panel |
| `chat-turn` with `data-role="assistant"` | An answer, as prose across the column |
| `chat-steps` inside an answer | Where the runtime writes the answer's query lines, as the Ask panel's steps, when not at its top |
| `chat-row` | Blocks side by side in one answer, such as two charts read together |
| `data-height="short\|medium\|tall"` on a tag | A height of 16, 20 or 28rem instead of the default, 24rem for a chart and 20rem for a table. When a KPI's figures or a Text chart's words need more room, the card grows to show all of them |

An answer may hold paragraphs, lists, `h3` subheadings, `blockquote` and `code`, as a chat answer would.

## What a reader can do

- Open any query line to see what ran: its workspace and model, the filters that applied, the rows, when it ran, its Kater SQL and **Open query**, which opens the query on its workspace's page.
- Use each card as on a dashboard: hover a mark, open its details and its Data tab.
- Rerun the thread for another value, when the page has a `<kater-filter>` in its `chat-header`. Changing it runs every answer again, which turns the conversation into a check anyone can repeat for a platform, a region or a period. Add one only when every answer's words hold for every value it offers.
- Carry on elsewhere: **Edit → Copy as Markdown** copies the thread with the figures on screen, each question as a heading, ready to paste into another chat. **File → Export** writes it as a PDF, a Word file, Markdown, or an Excel workbook of its figures. A copy leaves out the query lines and ends with the sources list.

## Folder

A thread is an app like any other ([apps](vfs://guides/app.md)). Create it with `kater new app NAME --workspace WORKSPACE --label LABEL` through `kater_apply` once the person agrees, with a snake_case name and one of the person's own workspaces. It then lives in their `users/{email}/apps/{name}/`, beside their workspaces, whichever workspaces its queries come from; a connection workspace as the target would put it in that workspace instead. Replace the starter `index.html` with the thread, and tell the person the app's name and where to open it. Sharing it runs the [sharing check](vfs://guides/app-pages.md#sharing): everyone it's shared with must be able to read every query it names.
