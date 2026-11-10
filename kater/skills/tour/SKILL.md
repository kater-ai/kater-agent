---
name: tour
description: "What the connection holds, and three questions it answers now. Use it when the person asks: give me a tour, what can I ask."
---

This skill is Kater's guide `vfs://guides/tour.md`.

# Tour the data

Walk the person through what this connection holds, in their words, then answer three real questions from it. A tour reads and runs; it writes nothing outside the brainstorm folder.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

Use this for a first look at the data, or at a part of it the person hasn't used. For what changed lately, use [brief me](vfs://guides/brief-me.md). To model a table nothing models yet, use [model table](vfs://guides/model-table.md).

## Procedure

1. **Read the context.** `connections/<connection>/connection_context.md`, the root and the person's `about.md`, and the `focus` they gave.
2. **Take stock, briefly.**
   - **Shared models:** `kater model list --select connections/<connection>/modeling/kater_objects/semantic_models` names each shared semantic model with its `topic`, `team` and `role` tags. Read the labels and descriptions of the ones that fit with `search_catalog` (kinds `model`), not their fields.
   - **Workspaces and apps:** `kater invoke workspace.browse` and `kater invoke app.browse` with `show: all`.
   - Pick the two or three models that fit the focus, or the person's metrics, best.
3. **Say what's here** in a few lines of business words: what each of those models covers, how fresh it is when `connection_context.md` says, and the workspaces and apps built on it. Never list every field or file.
4. **Find three questions it answers now,** each from a verified definition. Look, in order, at saved queries that return its verified fields, then the model's Explore tour (`kater explore tour MODEL --scope connections/<connection>/modeling`: its Headlines and Questions), then its `business_questions`. Pick the three that matter most to this person.
5. **Answer each one.** Run it once, `kater run --select QUERY_PATH` or the Explore card's preview, and give the answer in one line with the result it came from. Never type a figure in.
6. **Say what's thin,** in one line: a model with no description, or a metric the person watches that nothing defines, and who could fix it.
7. **Point to what's next:** show an answer in Kater in a new tab (`kater open --new`), `/brief-me` for what moved, or `/compare` to set two periods side by side.

## Stop conditions

- The connection has no shared models yet: say so. A Developer or an Admin starts with `/model-table`.
- A run fails: report its error plainly and pick another question, once.
