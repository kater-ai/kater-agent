---
name: send-feedback
description: "Send feedback. Use it when the person asks for: Something wrong in Kater, or something it should do."
---

This skill is Kater's guide `vfs://guides/send-feedback.md`.

# Send feedback

When something in Kater goes wrong, or the person wishes for something that is not in Kater, send it to Kater with `kater_send_feedback`. One call sends it. A bug goes to Kater's engineers at once. A feature request or a question goes to Kater's team, who read every one. Feedback is not content: it goes to Kater, and nothing is written in a connection.

## When to offer it

- **A bug: something in Kater goes wrong.** A page, a chart, a command or a tool gives an error, an order or an answer that it should not give. Examples: a chart sorts months in alphabetical order, or `kater git push` fails on a file name with a space.
- **A feature request: the person wishes for something that is not in Kater.** Examples: "I wish I could get the dashboard by email as a PDF each Monday", or "Can I see this as a map?" when that is not possible. First check with `kater start` and Kater's skills that it is not possible already. When it is possible, do it, and do not send a request.
- **A question about Kater** that you cannot answer from the guides, or when the person is not sure what it is: send it as `other`.
- **Not for the person's own data or work.** A wrong figure in the warehouse, a definition that is missing or a query you can fix is not feedback. Help with those as usual.
- **Look first.** For a bug, read the tab in front with `kater here`, and run what the person ran, so you can say what happens and why.
- **Find what the organization already sent.** Before you offer feedback, list the open reports with `kater report list` (add `--type feature_request` for a request). When one asks the same, do not offer a new one: tell the person its id and title, and offer to add what is new to it with `kater report comment ID --text "..."`, through `kater_apply`.

## Ask first, then send it in one call

Ask the person in words before you call the tool, with the title and one line of what you send. For example: "This looks like a bug in Kater. Do you want me to send it to Kater's engineers?" or "That is not in Kater yet. Do you want me to send it to Kater's team as a feature request?" Call the tool only after they say yes. Their chat may ask them again before the tool runs. That is the same yes, so do not ask again after it.

```json
{
  "kind": "bug",
  "title": "Bar chart sorts months in alphabetical order",
  "what_happened": "The revenue_by_month chart shows the months as Apr, Aug, Dec, Feb, Jan. The Results tab shows the same rows in date order.",
  "expected": "The chart shows the months in date order, from Jan to Dec.",
  "steps": "1. Open the finance/revenue workspace.\n2. Open the revenue_by_month query.\n3. Select the Chart tab.",
  "agent_findings": "The month field is text, such as Apr 2026, so the chart sorts it as text. A sort on month_start gives the correct order.",
  "impact": "workaround",
  "ran": [{"what": "kater run", "request_id": "7f3a91c2"}]
}
```

- `kind` is `bug`, `feature_request` or `other`.
- `title` is one sentence. `what_happened` is what the person saw, in their words. For a feature request, it is what they want and why.
- Put what you found, such as the cause or a fix, in `agent_findings`. Engineers read it first. Keep it out of `what_happened`.
- `expected` and `steps` are for a bug, when you know them.
- `impact` is `stops`, `workaround` or `small`, from what the person says.
- `ran` names each query you ran for it, with the request id its answer gave, and `page_urls` each Kater page it is about. At most 5 of each.
- Write short sentences, and use the names that show in Kater: workspaces, queries, fields, tabs.

## What it attaches

- The Kater tab in front, when the person shares their tabs with their agents.
- Each query in `ran` and each page in `page_urls`.
- Where it comes from: the Kater MCP and the app your chat runs in.

Never attach rows from `kater answer`, query results, file contents, credentials or tokens, and never paste them into a field. Name the query and its request id. The engineer runs it again.

## After it is sent

- The tool answers the report's id, its link and one sentence on what happens next. Tell the person that in one sentence, with the link.
- The same title sent again within 10 minutes is the same report: nothing new is sent, and the answer says so. Do not send it again.
- When an open report of the organization asks the same, the tool sends nothing and names it, such as "Your organization already sent a report that asks the same: BUG-1001". Its error has the code `already_sent` and lists each such report in `open_reports`, with its id, title and status. Tell the person, and offer to add what is new to it with `kater report comment BUG-1001 --text "..."`. Only when they say theirs asks something else, call the tool again with `new: true`.
- When the tool refuses, tell the person its words. An organisation can keep feedback to its admins or its data team.
- The person hears about each change by email, or by Slack DM if their team uses Kater in Slack. You do not need to check it again for them. When the person asks, `kater report show ID` gives its status and activity, and `kater report list` lists their organization's reports, in the queue's order. A report is Sent, Reviewing, Done (it is fixed) or Closed (it closed without a fix).
- Only when the person asks, and through `kater_apply`, change a report they sent, or any report when they are an admin: `kater report close ID --comment WHY` when they no longer need it, `kater report reopen ID` to open a Closed report again, and `kater report severity ID --impact LEVEL --reason WHY` when it matters more or less now. A higher level needs the reason. A Done report stays Done: if the bug is back, the person selects It still happens on its page.
