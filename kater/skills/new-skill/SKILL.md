---
name: new-skill
description: "Write a skill of the person's own that they start by name. Use it when the person asks: make my own command, a shortcut for a request I repeat."
---

This skill is Kater's guide `vfs://guides/new-skill.md`.

# Make your own command

Write a skill of the person's own: a short procedure that the agent follows whenever they type its name, in the Ask panel or in their own agent. It lives in `connections/<connection>/users/<email>/ai_tools/skills/<name>.md`, where only they change it, and the Agents page lists it under Personal.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

Use this when the person repeats the same request, or asks for a command of their own. A procedure everyone should get belongs in the connection's `ai_tools/`, which the data team changes. A procedure that should run on its own, such as every Monday, is a scheduled workflow, not a command: use [digest](vfs://guides/digest.md) for a recurring brief, [watch](vfs://guides/watch.md) for a metric that crosses a line, or [schedule workflow](vfs://guides/schedule-workflow.md) to schedule any workflow ([workflows](vfs://guides/workflows.md)).

## Procedure

1. **Learn what it does.** Ask, one at a time and only what you can't infer: what it's for, what they'll say to start it, what it needs from them each time, its steps, and what a good result looks like. When they make it from a chat, start from what they just did with you.
2. **Reuse before writing.** `kater ai-tools list` lists every skill they can use. If one already does this, say so and stop. When theirs builds on one, it names that skill rather than copying its steps.
3. **Name it** in lower case with `-` between words, a letter first and at most 64 characters, short, a verb where one fits, such as `weekly-roas` or `check-spend`. A name the connection's AI tools have would hide that one for them, so pick another.
4. **Draft it** in the shape below.
5. **Write it, then tell.** It is their own file, so write it with no question to `connections/<connection>/users/<email>/ai_tools/skills/<name>.md`, with a `summary` such as "New command /weekly-roas". Show what you wrote, and say they can change it or say undo. The Ask panel loads it, and each change to it, from their next new conversation.
6. **Say how to use it:** `/<name>` in Ask, on the Agents page under Personal, and in their own agent's prompts, where it shows as soon as it's written (`/mcp__kater__<name>` in Claude Code). Offer to run it once now.

## The shape

````markdown
---
label: Weekly ROAS
name: weekly-roas
description: Brief me on return on ad spend by platform for the last full week.
starter:
  title: Weekly ROAS
  summary: Return on ad spend by platform, last week
  icon: chart-line
  ask: Brief me on return on ad spend by platform for the last full week.
  inputs:
    - name: since
      description: How far back to look. Left out, the last full week.
---

# Skill: weekly-roas

One sentence: what it does.

## Procedure

1. Each step in one or two plain sentences, naming each field, query or workspace by its full path, and the skill or guide it follows.

## Stop conditions

- When to stop and ask.
````

- **`starter`** makes it a command. Its keys:
  - `title`: what they pick, in their own words. The skill's `label` stays its name among the AI tools.
  - `summary`: one line under the title.
  - `icon`: a Kater object's kind, drawn with its own mark and colour (`app`, `workspace`, `measure`, `semantic`), else one of these [lucide](https://lucide.dev/icons/) icons: `arrow-right-left`, `badge-check`, `bell`, `book-open`, `calendar-clock`, `chart-column`, `chart-line`, `chart-pie`, `compass`, `flask-conical`, `lightbulb`, `list-checks`, `map`, `megaphone`, `newspaper`, `pencil-line`, `search`, `shopping-cart`, `sparkles`, `square-slash`, `target`, `trending-down`, `trending-up`, `user`, `users`. Any other name wears the skill's own mark.
  - `ask`: the words it starts with, after `/<name>`. Words that stop mid-sentence, such as `Why did`, wait for the person to finish them.
  - `inputs`: what they can say each time, each a `name` and a `description` that says what happens when it's left out. `required: true` only when the command can't ask for it.

  It has no `group`: their own commands are under Yours.
- **Its steps follow the rules** every answer does (the company's `rules.md`): figures only from runs, drafts in the brainstorm folder, nothing written anywhere else without a yes.
