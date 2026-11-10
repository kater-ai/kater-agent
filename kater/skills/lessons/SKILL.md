---
name: lessons
description: "Lessons. Use it when the person asks for: A lesson for the next session: \"remember\", \"from now on\", a correction, or what worked after a refusal or a long search."
---

This skill is Kater's guide `vfs://guides/lessons.md`.

# Lessons

A lesson is what you learn while you work that makes the next session better: fewer calls, fewer refusals, and answers the way the person wants them. Every later session reads it at its start, so keep each lesson short, put it in one place, and record only what lasts.

## When to record a lesson

Record a lesson when one of these happens:

- The person corrects you, or says "remember", "from now on" or "always".
- A call is refused or fails, and you find what works.
- You needed more than two extra calls to find something, such as the right guide, a path or a field's name, and the answer is the same next time.

Record nothing that holds only for this conversation, nothing that a file or the catalog already says, such as a field's definition or why two sources differ when a field's description says so, and no figure that will change, such as last week's email revenue.

A request is not a lesson. When the person asks you to send a field they made to the data team, such as "send it to the data team to check" after you wrote a definition of email revenue in their query, that is a promotion request: `kater promotion request PATH FIELD --reason TEXT`, run through `kater_apply` once they say yes, as `vfs://guides/model-layer-rules-promotion.md` says. Never send it as a proposal of a file, and record no lesson about it.

## A lesson is one line

What to do, then why, in plain words: "Round revenue to whole dollars: the person reads cents as noise." Never put rows, figures from a result, credentials or tokens in a lesson.

Before you add a line, read the file. When a line says the same thing, change that line. When a line no longer holds, replace it. Never keep two lines that disagree, and never start a second file.

A lesson file has a size limit, because every session reads it: a person's `about.md` holds at most 4,000 characters, and a `lessons.md` at most 2,000. A write over the limit is refused with the code `too_long`, such as "Your about.md would be 4,310 characters, over 4,000." Then rewrite the file shorter: merge the lines that say the same thing, remove what no longer holds, and write it again. Never move lines to a second file.

## Where each lesson goes

A lesson goes in one place. When you cannot tell whether it is about the person or the company, it is the person's. When its place refuses it, such as a proposal that is refused or a report to Kater that cannot be sent, tell the person in one line with the refusal's words, and record it nowhere else: a lesson in the wrong file misleads every later session.

| The lesson is about | It goes in | How |
|---|---|---|
| The person: what they want, how they work, their words | Their `about.md`, under the heading it fits | Change, then tell |
| The person's own repeated steps | A skill or a workflow in their own `ai_tools/` | Change, then tell |
| What the company's data means | The connection's `connection_context.md` | A proposal |
| A field or a filter the person made, for everyone | Not a lesson: the field's promotion request | `kater promotion request` |
| A rule of the company | `rules.md` at the content root | A proposal |
| How to use one of Kater's skills in this organisation | The skill's `lessons.md`, in the connection's `ai_tools/` | A proposal |
| The shipped text is wrong or missing: a guide, Kater's instructions, a tool's description or answer | A report to Kater | `kater_send_feedback` |

The person's `about.md` is `vfs://connections/{connection}/users/{email}/about.md`, where `{email}` is `person.email` of `kater_start`. Their own AI tools are in `ai_tools/` beside their `brainstorm/` folder. [About me](vfs://guides/about-me.md) says under which heading of `about.md` a line goes.

A lesson that only one person needs goes in their own files, never in the company's. A lesson about how one skill does its work, such as "every deep analysis compares a week with the same week last year", goes in that skill's `lessons.md`, even when the whole team works that way: `rules.md` holds only what every answer follows.

## A lesson about one of Kater's skills

Kater's skills are Kater's text, in `vfs://guides/`, and nobody in an organisation changes them. The organisation's lessons for one are `lessons.md` in the connection's `ai_tools/`, in the folder named for the skill: `connections/{connection}/ai_tools/skills/{name}/lessons.md`, such as `skills/brief-me/lessons.md` for [brief me](vfs://guides/brief-me.md). The lessons are the organisation's, and no release of Kater changes them. `kater ai-tools list` names a skill's `lessons` when it has some: read them with the skill. To add a line, propose the whole file, the lines it holds already too, as below.

## Change, then tell: the person's own files

The person's `about.md` and their own `ai_tools/` are theirs, so you change them with no question: write them with the `kater` tool (`source write PATH --text-file -`), `write_file` or `edit_file`. Another person's files, and every file outside the person's folder, stay refused.

The write answers `change`, with its `undo`. Tell it as "After you record a lesson" says, with that `undo`: "I noted in your about.md that you round revenue to whole dollars. Undo: `kater undo ch_…`." When the person says undo, run the change's `undo` with `kater_apply`. The change shows in the file's History, as every change does.

## A proposal: the company's notes

The company's notes are shared, so you do not change them. Propose the change, and the people who may write the file apply or dismiss it from their inbox. Only these take a proposal: `rules.md` and `about.md` at the content root, a connection's `connection_context.md` and a skill's `lessons.md`. Any other file is refused (`not_proposable`): a field or a filter goes to the data team as a promotion request.

```
kater propose PATH --text-file - --why "One line: what the change is for"
```

- Send the whole file as you propose it, with your line in it, on stdin. Run it with the `kater` tool: it asks the person nothing, and it writes no content.
- `--why` says why, in the person's words, such as "Sally corrected it twice: refunds are not in revenue."
- One proposal of a file by a person stays open at a time. A second one replaces the first, so send the whole change again, the first one's lines too.
- When the file's last proposal by the person was dismissed, the answer's `last_dismissed` gives who dismissed it and why: tell the person. The same text as the dismissed one is refused with the reason (`dismissed_before`): never propose it again, and tell the person what the reviewer said.
- When the person may write the file themselves, it is refused (`can_write`). Tell them the change, and write it with `kater_apply` after they say yes. Its answer has an `undo`: tell the write as a file you wrote, with that `undo`.
- When a proposal went to the wrong place, such as a line for a skill's `lessons.md` proposed to `rules.md`, withdraw it with `kater proposal withdraw ID` through the `kater` tool, then propose it where it belongs. It leaves every reviewer's inbox, and nobody is told.
- `kater proposal list` shows the open proposals, and `kater proposal show ID` the people who decide.

## A report: the shipped text

Kater's instructions and the guides in `vfs://guides/` are the same for every organisation, and nobody in an organisation can change them. When one is wrong or misses something, send the lesson to Kater with `kater_send_feedback`, and Kater's engineers change it for every organisation:

- `kind` is `bug` when the shipped text is wrong, and `feature_request` when something is missing from it.
- `title` names the guide, the rule or the tool it is about, such as "measure.md names the wrong command for a ratio".
- `what_happened` says what the text told you and what you did. `agent_findings` says what worked.
- Never put the person's data, a figure, a file of the company or the company's rules in it.

Ask the person first, as `vfs://guides/send-feedback.md` says, and send it in one call after their yes. When the person asked you to tell the Kater team, such as "make sure Kater knows", that is their yes: send it.

## After you record a lesson

Tell the person once, in one line, what you recorded or proposed and where, in plain words. Never in the middle of an answer: the answer comes first, and a finding is told whole. The line is the last of your answer, after the links to what you made, and just before **Sources** when the answer ends with them. Record and tell each lesson once in a conversation.

- A file you wrote, the person's own or one they said yes to: what you noted, then its undo: "I noted in your about.md that you round revenue to whole dollars. Undo: `kater undo ch_…`."
- A proposal: "I proposed to the data team that the connection's notes say why Klaviyo counts more email revenue than Shopify."
- A report to Kater: "I told the Kater team that the measure guide names the wrong command."
