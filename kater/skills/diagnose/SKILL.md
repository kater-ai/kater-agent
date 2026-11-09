---
name: diagnose
description: "Why a figure changed. Use it when the person asks for: Why a figure changed, what drove it, what happened in some periods; A question about a chart, or a part of it the person selected, from a prompt that Ask AI copied."
---

This skill is Kater's guide `vfs://guides/diagnose.md`.

# Why a figure changed

A person asks why a figure moved, what drove it, or what happened in some periods. This is research, not a lookup. Work through it one step at a time, as an expert analyst would, and come back with an answer: the person asked a question and wants something to work from. Find out yourself everything that the data, the projects and the timelines can tell, and ask the person only what they alone know (below). They add what they know as you go. Accuracy comes before speed: a careful answer in five steps is better than a fast one that is wrong.

Never show the person a tool name, a file path, a command, an id or SQL, unless they ask for it, and never Kater's own words for its parts, such as scratch file, statement, brainstorm folder or workspace path. Say "I'll look at the campaigns behind it", not the command you run. What you keep is a query in their workspace: name both, as links. What the person sees in Kater keeps the name Kater shows them: an app, a dashboard, a workspace, a query, a chart.

## 1. Restate, plan and start

Before any call that reads data:

- **Say what you see,** in one or two sentences, with the figures the person or their prompt gives, as given: which figure, which periods, its value before and after, and what moved with it or held steady. Do not turn them into a multiple or a share, such as "halved".
- **Give your plan** in three to five short steps, in the person's words, such as: "First I'll check whether the volume or the rate moved. Then I'll look at the parts behind it. Then I'll look for a recorded change around that week, and check that the data is complete."
- **Start at once.** Do not stop for a question here. Each question you could ask, first try to answer yourself:
  - Was the period before normal, or a spike? Compare it with the periods before it.
  - Compared with which periods? The ones the prompt or the selection gives, else the period before.
  - Does the person know of a change, such as a launch or a budget move? Read what people recorded on the work (step 5) and the tools connected to the chat.
  - Which of two meanings? Take the likely one, say which one you took, and name the other.

**Ask only when you cannot go on without the person,** such as when two readings of the question need different work and nothing tells you which, or a step changes what other people see. Then ask with your chat's question tool, each question with two to four short options and the one you recommend first, and every question of one step in one call, never as a list in your message (with no such tool, ask in one short message), and do the checks that do not depend on the answer first. A question the data can answer is never one to ask. What only the person can confirm, such as why a budget changed, goes in your answer as what would confirm it, not as a question before it.

A question with one reading, such as "what was spend last week", is a lookup: answer it, with no plan and no question, and with the period before and the change ([A figure over a period](vfs://guides/sandbox.md#a-figure-over-a-period)).

**A why about a figure the person just saw,** such as "Why did it go up?" after a lookup, already has its figure and its two periods. Do not ask what it is compared with: it is the period before that you gave. Show the first cut at once, then go on:

1. Say in one sentence what you look at first: the figure by its main part, such as each platform, channel or product, for the two periods.
2. Run that one query, with the change for each part, and show it with its chart.
3. Say in one line what it shows, with the figures as the result gives them, such as which parts moved and which held flat.
4. Go on from step 2 below with the part that moved most, and look for what people recorded, with no question in between: the person asked why, so answer why.

Never answer a why with a plan and questions and no figure.

## 2. Work in steps

For each step of the plan:

1. Say in one plain sentence what you check in the data and why. Say nothing about how you use Kater, such as reading a guide or fixing a request.
2. Check it (below).
3. Show what you found: the chart or the figures, and in one line what they mean.
4. Say what you check next and why, and go on. Do not stop between steps to ask whether to go on.

Keep going until the plan is done or the data cannot say more, then answer (step 6). When a step finds something you did not expect, say so and change the plan.

## 3. Read the data

- **Start from what is defined.** When the figure comes from a saved query, read its file for how it is computed: for a ratio, its numerator and its denominator, and its filters. Read the `workspace.sql` of its workspace for the shared model it reads and each `REQUIRED_FILTER`. `kater query fields <workspace>` lists the fields and their operations.
- **Write each check as a query in a workspace of the person's, from picks.** Take one of their own workspaces that reads the shared model behind the figure (`list_accessible_workspaces`); when none of theirs does, make one on that model with create_workspace, named for the subject, such as `tiktok_revenue`. Never add a query to a workspace that is not theirs. Write each query with `kater query fields <workspace>`, then `kater query new <workspace> --request-file -`, as the query_authoring skill says: it is saved at once, as a new query that changes nothing that exists, and the answer cites it. Put each `REQUIRED_FILTER` into the conditions, so the rows are the figure's rows. Keep the labels and formats the figure is shown with: a label is `derive.map`, never a `CASE`.
- **A scratch statement is only a try.** When you are unsure what a field gives, try it in a scratch statement ([Writing a statement](vfs://guides/sandbox-scratch-files.md#writing-a-statement)), then write the query. Never quote or cite a scratch statement's figures.
- **What to read:**
  - The figure and each of its inputs by period, for the periods in question and as many before.
  - The same one level down, such as each campaign or product, for the part that moved most.
  - Each input's change from the period before, with `comparisons.previous_period` and `output: percent_change`.
- **A ratio the model does not hold** is a pick such as `{"expression": "EVAL(sum__revenue) / NULLIF(EVAL(sum__visits), 0)", "output": {"name": "revenue_per_visit", "label": "Revenue per visit"}, "reusable": true, "format": "currency('USD', 2)"}`. Say that it is not verified. When the person wants others to use it, offer to ask the data team to add it to the shared model with `kater promotion`.
- **Type Kater SQL only when no pick makes the column,** with `kater new query <workspace>/NAME --sql-file -`, and say which pick you tried. Each figure from it is not verified: say so where you quote it and in its source.
- **Run and show.** `kater run --select <query path>` runs a query, and `--select` takes several paths at once. The run draws each query's result as Kater's chart, so the person sees what you found, not only your words. In a chat that shows no views, give each result's link, copied whole, and never build a link.
- **Quote each figure and each change as a result gives it,** with its period. Never work out a share, a multiple or a change yourself: add it to the query with `kater model edit`, or write one more query, and run again.

## 4. Test each likely cause

Name each cause the data could show, and check it:

- **Volume:** did the inputs change, such as spend, visits or orders, and by how much each?
- **Mix:** did the parts change, such as new campaigns, products or channels, or a shift of weight between them?
- **Rate:** did a rate change, such as orders per visit or value per order?
- **Measurement:** did tracking or attribution change, such as a count that falls near zero or a source that stops sending?
- **Data:** are periods missing, late or partial, are counts too small to trust, or do currencies mix? A ratio over a count of one or two is not a figure to quote: say so.
- **Calendar:** a holiday, a season, a launch, or a period of a different length.

Say which causes the data supports, with the figures, and which it rules out. A step that starts in one period and stays is a likely action, such as a budget change or a launch: say that you infer it from the data.

## 5. Look for what people recorded

- **In Kater:** `kater project list --app <app folder>`, `kater timeline read <app or project folder> --since <date>` and `kater history <folder>` show the decisions, notes, snapshots and changes on the work. Say what you found as the person's work: "Nobody recorded a decision about this budget on the app or its projects." A change to the app's files after the periods in question is not about them: leave it out.
- **Outside Kater:** when other tools are connected to the conversation, such as Slack, email, a calendar, Notion, Google Drive or an ad platform, search them for the periods and the names involved, such as a campaign. Say what you searched and what you found, with links. When nothing else is connected, say that a message or a plan from that week could confirm the cause.

## 6. Answer

Write in plain business words, and keep it short:

1. **The cause, and how sure you are:** "most likely", "possibly", or "the data cannot tell". Say what you assume.
2. **Three to five figures behind it,** each with its period, and each a link to the result it came from, as Kater's instructions say: `[$12,286](app_url)` with the run's `app_url` for that query, copied whole. A figure from a recorded note names the note and its date, and links the page its read gives.
3. **What you ruled out,** in one line.
4. **What would confirm it,** such as a person who knows of the change, or one more check. This is where the questions you did not ask go, as what the person can add: "If you know why the budget changed, tell me and I'll check it against the figures."
5. **What could help next,** as an offer (below).
6. **Sources,** last: one line for each query you quote, named in words as a link with the `query_url` its run gives, its workspace, its period and filters in words, and the newest date in its rows. A query written as SQL says "not verified". The person checks each figure there.

Leave out what the person did not ask about.

## 7. Offer what helps next

Offer one thing at a time, and only what a tool can do. Choose it by what the person asked and what they will want next, and say what they get from it:

- **To see whether it recovers:** a project with a goal and its metrics, which shows each week where the figure stands ([Projects](vfs://guides/projects.md)). "Want me to track this each week and tell you when it gets back to where it was?"
- **To share what you found:** a dashboard with the charts and the story in text ([Dashboard](vfs://guides/dashboard.md)), or a note where the team already looks.
- **To check one more cause:** the next query, or `kater compare` for two periods, which can take minutes.
- **To share a measure you made:** ask the data team to add it (above).

Make each one only after a yes, with `kater_apply`, and give the link that its result answers. After each change, say in words that you can take it back if they ask.
