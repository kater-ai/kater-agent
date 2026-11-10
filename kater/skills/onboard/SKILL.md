---
name: onboard
description: "A first chat with someone new: write their about.md, pin their metrics, show one number. Use it when the person asks: set me up, onboard me."
---

This skill is Kater's guide `vfs://guides/onboard.md`.

# Onboard someone new

A first chat with someone new. When it's done, their `about.md` says who they are, and they've seen one real number they care about. Keep it to about five minutes, one question at a time.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

Use this when someone is new, asks to be set up, or their `connections/<connection>/users/<email>/about.md` has no words under its headings. To change one fact later, use [about me](vfs://guides/about-me.md). Setting up the connection itself, its warehouse and first models, is an admin's `kater init`, then [model table](vfs://guides/model-table.md). Adding the organisation's Anthropic key, so the portal's Ask panel answers, is an admin's `kater init ai-key` or AI settings: never ask anyone for a key in the chat.

## Procedure

1. **Read first, without asking.** Their `connections/<connection>/users/<email>/about.md`, the root `about.md`, `connections/<connection>/connection_context.md`, and what they own or opened lately: `kater invoke workspace.browse` and `kater invoke app.browse` with `show: mine`. Note what each heading of their `about.md` already says.
2. **Say what you see.** Greet them by first name, and in a line or two say what you can already tell, such as the team their workspaces belong to. Invite a correction.
3. **Ask four things, one at a time,** skipping any their `about.md` already answers:
   - **Role:** what they do, and for which team.
   - **What they watch:** the three to five numbers they check most. Find each one with `search_catalog` and say back what you found in their words, such as "sessions, from the marketing sessions model". A number nothing defines stays out, and you say so.
   - **How I like answers:** short or detailed, a chart or a table, plain words or SQL.
   - **Who I work with:** who they report to, and who they share work with.
4. **Write it once, then tell.** With no question, write `about.md` and pin each metric, as [about me](vfs://guides/about-me.md) says under "About file rules": only the four headings, Projects I follow left as it is for their Daily brief, and each write's `summary` the fact learned in their words. Then say in a few lines what you wrote and pinned, and that they can change any of it or say undo.
5. **Give a first answer.** Take the first metric they named that has a verified definition: a field `explain_field` reports verified, read from the shared models or from a saved query that returns it unchanged. Run it by week for the last eight weeks, with the query that returns it or a new query in one of their own workspaces (`kater query new`, then `kater run --select QUERY`), and show the result with where it came from. Never type a figure in. When none of their metrics has a verified definition, say which ones lack one, and offer [build query contract](vfs://guides/build-query-contract.md) to define the first.
6. **Point to what's next,** a line each: their Daily brief in Kater, which sends what stands out each morning, `/brief-me` for what moved, `/tour` for what else the data answers, and `/about-me` to change anything later. They can ask in the portal's Ask panel or in their own agent with the Kater MCP connected.

## Stop conditions

- They'd rather not say: write what they did say, leave the other headings empty, and go on to the first answer.
- `about.md` can't be written: say why, and show the text so they can paste it into About you.
