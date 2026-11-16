---
name: about-me
description: "Remember or change a lasting fact about the person in their about.md. Use it when the person says: remember this about me, update what you know."
---

This skill is Kater's guide `vfs://guides/about-me.md`.

# About me

Record what the person tells you about themselves in their `about.md`, in their own folder that `connection.folder` of `kater_start` names, such as `vfs://connections/<connection>/users/<email>/about.md`. When `kater_start` names no `connection`, ask the person which connection they work in. It is read at the start of each conversation, in the Ask panel and in their own agent, so one good line here changes every answer after it.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

Use this when the person asks you to remember something about them, or corrects what you know. Use it unasked too, whenever they state a lasting fact in passing, such as "I only care about paid channels": write it, and say so in one line. For a whole first setup, use [onboard](vfs://guides/onboard.md). A lesson about the company, its data or one of Kater's skills goes somewhere else: vfs://guides/lessons.md says where each lesson goes.

## Procedure

1. **Read it first.** Their `about.md`, and what each heading says now.
2. **Get the fact.** From their words or the `fact` input. With neither, show what the file says in a few lines and ask what to add or change.
3. **Place it.** Pick the heading it fits: Role, How I like answers or Who I work with. A number or a project they care about is a follow, not a line in this file: see below. A fact about everyone, such as when the fiscal year starts, is the company's: propose it, as vfs://guides/lessons.md says.
4. **Write it** by the rules below, with no question, changing only what the fact changes. A fact that contradicts an older one replaces it; never keep both.
5. **Say what changed** once, in one line with the write's `undo`, such as "I noted in your about.md that you round revenue to whole dollars. Undo: `kater undo ch_…`." When you noted it unasked while you answered something else, the line is the last of your answer, after the links to what you made, never in the middle of it, as vfs://guides/lessons.md says. When they say undo, run the write's `undo` with kater_apply.

## About file rules

- **Only the three headings,** `## Role`, `## How I like answers` and `## Who I work with`, in that order. Keep every heading, an empty one too, and add no others. Under a heading, write plain sentences or a list.
- **A number they care about is a follow.** Look it up with `search_catalog`, and when two fields could be it, ask which. Find a query that returns it, then follow it with `kater follow add QUERY --figure FIELD[:ROW] --label WORDS`. It shows in Memory under Following and in their Daily brief, and they hear when it meaningfully changed. With no query that returns it, say so, and offer to write one. See [follow](vfs://guides/follow.md).
- **A project they care about is a follow too.** Find its folder with `kater project list`, then follow it as [follow](vfs://guides/follow.md) says. Its figures are then in their Daily brief, and the project shows in Memory under Following.
- **Each write's `summary`** is the fact learned, in their words, such as "Weeks start on Monday" or "Reports to the paid media lead", never "Updated about.md". Memory lists every summary as a line under Learned, with Change and Forget, so write one fact per write when you can.
- **Only what they offered** about their work: no personal details they did not give, no credentials, no rows.
- **At most 4,000 characters,** since each conversation reads it at its start. A write over that is refused: merge the lines that say the same thing, remove what no longer holds, and write it again, never a second file.

## Stop conditions

- The file cannot be written: say why, and show the text so they can paste it into About you.
