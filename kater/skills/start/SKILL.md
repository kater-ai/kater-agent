---
name: start
description: "Starting something. Use it when the person asks for: \"What can I do with Kater?\", a goal such as a target or a weekly update, or something new to make with choices to ask about."
---

This skill is Kater's guide `vfs://guides/start.md`.

# Starting something

A person starts in one of two ways: from a goal, such as "get spend to a target by June", or from a thing to make, such as a deck. Kater keeps both in one list, the same one the New dialog in the portal shows. Read it with `kater start`. Never write the list from memory.

## The commands

- `kater start` lists the goals and what you can make: each kind with its types. In a host that draws Kater's view, its answer is a card, and a click sends the person's words as their next message.
- `kater start KIND TYPE` answers a type's choices, such as `kater start app deck`: each choice, its options with the recommended one first, the first line of the request, and the `kater new` line that makes it. `kater start KIND` with no type answers the kind's types. In a host that draws Kater's view, its answer is a card with one question for each choice and one button.
- `kater start goal --shape SHAPE` answers what the person already has for a goal: the measure it is about, the dashboards whose queries read it, and the apps and workflows that read those dashboards. Give the measure with `--metric`, by its name or its label, such as `--metric spend`, and, for a goal with a source such as `update`, the dashboard, query or app with `--set source=PATH`. It reads files and compiled metadata only, and runs nothing on the warehouse.

## Ask only what the prompt did not set

1. Read what the person wrote. Set each choice that their words decide with `--set CHOICE=VALUE`, such as `--set numbers=static` for "the numbers as they are today". A schedule in their words, such as "each Monday at 8", is the schedule choice, in their time zone.
2. Run `kater start KIND TYPE` with those values. A choice it answers with `set` is decided: never ask about it again. A choice with `hidden_by` does not apply, such as Schedule for a static deck.
3. Ask one question for each choice left that changes what you make. Give its options with the recommended one first, and say that it is recommended. Ask all of them in one message. When the host can ask with a form or with options, use that.
4. A choice the person does not care about takes its recommended option. Do not ask about it when the request already tells you enough to make something good.
5. Make it with the `kater new` line the answer gives, with the values you set.

Never ask about the person's time zone. A schedule's words name it, such as "8:00 AM New York time": give it to `kater start` as `--timezone America/New_York`, and the `kater new` line it answers carries it. When the words name none, leave it out, and Kater takes the person's own.

When the person selects options in Kater's card, their message is the first line of the request with every choice in it. Make the object from it without a question about those choices.

## Source data

Before you ask what an object is about or what it reads, look at everything the person can read, in one step: each place the Source data choice's `find` names. That is their dashboards and queries, the shared models and datasets, their apps, and their projects, which say what they work on now. Never look only at dashboards. Offer the sources that fit the person's words as options, a project's by its name. Then offer each of the choice's `also`: Find the best fit for me first, which is recommended and leaves the flag out, and Use mock data last.

Use mock data makes the object now, without waiting for data: each figure that Kater has comes from Kater, and each one it does not have gets mock data. That is the person's answer for each such figure, so do not ask again for each one: give it a mock data source with what you searched and the data that replaces it, as vfs://guides/app-sources.md#sources says. Every mock figure wears the MOCK DATA badge until real data replaces it.

## Start from a goal

Run `kater start goal --shape SHAPE` before you make anything for a goal. Then:

- Change or reuse what the person already has before you make something new. A dashboard that already charts the measure is a better start than a new one.
- Propose the plan in one question, with the options and the recommended one first, such as "Add a marker to Channel performance weekly (recommended), or make a new dashboard". Make nothing until the person says yes.
- When two measures fit the words, ask which one, with those two as the options. When one fits, use it.
- The answer names the starter that does the work, such as `/planning`. Follow it.

Projects, their metrics and their timeline are in vfs://guides/projects.md. Workflows and schedules are in vfs://guides/workflows.md.
