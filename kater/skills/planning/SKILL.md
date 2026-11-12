---
name: planning
description: "Plan a goal in phases, or an analysis that needs more than one query. Use it when the person asks: help me plan, a project with goals."
---

This skill is Kater's guide `vfs://guides/planning.md`.

# Workspace Planning Reference

## Purpose

Use this guide when a question needs more than one governed operation, a new or changed object, profiling of an unknown parameter, a conditional diagnostic, or future recurring execution. The result is one evolving artifact, a topic's note, `brainstorm/<topic>.md`, in the current user's [brainstorm folder](vfs://guides/brainstorm.md), never a second free-standing plan document. A goal that takes several changes, rather than one question, is a plan in phases instead: plan it with the person as [Plan a goal with the person](#plan-a-goal-with-the-person) says, in the shape vfs://guides/plans.md gives.

## Preconditions

[Resolve workspace](vfs://guides/resolve-workspace.md) selects one exact readable workspace before planning. The planner records its `${workspace:...}` ref, exact path, personal-or-connection status, one typed selected_source identity, catalog revision, reusable configuration, and contract inventory. A plan can inspect connection-wide modeling and readable connection workspaces for patterns; an execution DAG may reference only selected-workspace contracts and configuration.

## Planning sequence

1. Restate the business question, its governed source references, decision to support, constraints, and non-goals.
2. Inventory reusable connection-wide semantic models, selected-workspace definitions, query contracts, and compatible insight templates before proposing any new object.
3. Classify each missing item as connection-wide modeling, the reusable workspace root interface, a selected-workspace query contract, or a run-only invocation parameter. Threshold-like values are always invocation parameters.
4. State the approach in bounded operations with the evidence each operation contributes and the condition that stops further work.
5. Create one typed DAG. Every node has an exact governed operation, explicit dependencies, declared parameters, a run condition, a bounded output, and a status. Unknown parameters require a `profile_query` followed by `decision`; follow-up diagnostics require an explicit condition and bounded finding keys.
6. Create one object-status row for every object used or changed. The Developer owns the build rows; the Analyst does not author missing objects while executing the DAG.

## Required artifact sections

After its `# Title` and `Status:` line, the artifact contains these sections in this order: `## Request`, `## Workspace resolution`, `## Question`, `## Approach`, `## Analysis DAG`, `## Object status`, `## Profiling`, `## Parameter decision`, `## Execution evidence`, `## Findings`, `## Recommendations`, and `## Review`.

The artifact stores object references, request-scoped workspace selection evidence, parameters, run identifiers, bounded summaries, decisions, uncertainties, and validation outcomes. It never stores credentials, raw result dumps, hidden reasoning, or a computed transformation the governed query did not return.

## Analysis DAG

| Node | Type | Depends on | Governed operation | Parameters | Run when | Produces | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `profile-peer-distribution` | profile_query | — | `${query:peer-distribution}` | selected date range | always | distribution and coverage statistics | planned |
| `choose-sensitivity` | decision | profile-peer-distribution | analysis checkpoint | candidate values | no value supplied | selected provisional parameter | planned |
| `detect-outliers` | insight | choose-sensitivity | `${insight_template:outlier_detection}` over `${query:outlier-analysis}` | declared method and sensitivity | parameter selected | bounded findings | planned |
| `diagnose-outliers` | diagnostic_query | detect-outliers | `${query:outlier-diagnosis}` | approved finding keys | material findings exist | volume-versus-depth evidence | planned |

The valid types are `profile_query`, `decision`, `build`, `analysis_query`, `insight`, `diagnostic_query`, `verification`, and `report`. A node is complete only after the named tool succeeds and the artifact records its bounded evidence. Insight output cannot become arbitrary input to another operation; a diagnostic branch only receives the approved finding keys and declared parameters recorded at its decision checkpoint.

## Parameter calibration

Profile through governed query contracts. Use returned aggregate evidence to describe sensitive, balanced, and conservative candidates, including expected finding volume, coverage, stability, and tradeoff. Do not calculate missing quantiles, rankings, joins, or totals in memory. An interactive run stops at the decision node; an autonomous run needs an already-authorized policy and marks the selected value provisional. Store the selected value and rationale as run provenance. A later calibration may recommend a different parameter but never changes a running or scheduled value without approval.

## Object status

| Object | Type | Status | Owner | Evidence required | Notes |
| --- | --- | --- | --- | --- | --- |
| `${query:example}` | query_contract | exists / needs-change / scaffolded / built | developer | compiler validation and bounded sample run | requirements or required change |

An `exists` row names the current compatible identity. A `needs-change` or `scaffolded` row is not runnable until the Developer records a successful build. Connection-wide changes retain their privileged authoring path; do not use [deep analysis](vfs://guides/deep-analysis.md) to create a duplicate semantic model merely because a workspace needs to select it.

For every planned output, state whether it inherits a source identity, declares a new named measure, dimension or custom calculation, or changes only an AS label. Record complete model/workspace namespaces in dependencies and bindings. Name any unnamed output that another analytical model must consume, and review all external dependents when changing a declaration name.

## Recurring checks

- Goals, Targets and Triggers are not built: no code reads or runs them. Do not write these files. For a recurring check, use a workflow (vfs://guides/workflows.md).
- Recommend only bounded actions a named individual can perform after required approval.
- Recommend useful recurring checks, but make arming opt-in. Seed a new threshold from a measured baseline, describe it as provisional, and offer “flag for later.”
- A schedule can send one saved query’s fresh figures to its owner in Slack. Follow [Schedule a Slack DM](vfs://guides/schedule.md), confirm the owner’s IANA time zone and connected Slack account, and save the file through `kater_apply` after approval. Writing an unpaused schedule arms it.
- A Slack schedule runs at each set time, including when its query returns no rows. It is not a conditional watch or a model-written digest. A watch, a workflow of kind `watch`, tells its owner only when a metric crosses its line, and a brief writes a digest each time (vfs://guides/workflows.md). A schedule file sends only to its owner. A dashboard's `schedule` sends to each person in `to` in Slack (vfs://guides/dashboard-file.md), and a snapshot workflow may post in one Slack channel. Email has no supported setup yet.
- Set delivery up only where the environment can arrange it. Unknown data cutoffs remain `null`; execution time does not establish data coverage. Do not report a saved draft, unavailable delivery setup or failed run as an active schedule.

## Object references (`${...}`)

- Write tracked objects as typed `${...}` refs whenever authoring or editing Markdown or governed YAML. Use `${workspace:name}`, `${semantic:name}`, `${kater_dataset:name}`, `${db_dataset:name}`, `${query:name}`, `${insight_template:name}` and `${doc:name}`: a model and a query take their own kind word. `${semantic_model:name}`, `${dataset:name}` and `${query_contract:name}` are refused. `${workspace:name}` reads as SQL reads a workspace's name: in a file of your own folder, your own workspace of that name or the connection's; in any other file of the connection, the connection's. When you and the connection both have a workspace of that name, the name names neither, as SQL refuses it: name it by its path, in a mention, as you name someone else's workspace. Write a field as `${model.field}`, a named workspace field as `${field_name}`, and a method as `${field.METHOD()}`. Write each reference outside backticks: a reference in a code span or a code block is only text, an example that names nothing, so no reader finds it, a rename leaves it as written and it is never refused.
- Each object's declared `name` is its identity, not its file path. Do not wrap warehouse paths, literal values, unknown columns, file paths, or question IDs.

## Plan a goal with the person

Use this when the person wants to get somewhere that takes more than one change, such as `/planning` or "help me plan": a new workspace and its queries, a dashboard, an app or report, a model change, a recurring check. Send one analytical question to [deep analysis](vfs://guides/deep-analysis.md) and one query to [build query contract](vfs://guides/build-query-contract.md) instead.

Work with the person, as a peer, to turn the goal into a desired state and a plan in phases. The plan is done when the developer could build P1 from it with no other context, and [run plan](vfs://guides/run-plan.md) could run every phase for the person. You facilitate; the person decides. The plan's shape is in vfs://guides/plans.md.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

### How to work with the person

- **One question at a time,** the sharpest one you can't answer from the content yourself. Never a questionnaire.
- **Look before you ask.** Read what exists, then ask about what it can't tell you: intent, priorities and taste.
- **Offer, don't interrogate.** Where there's a choice, give two or three options in a line each, say which you'd pick and why, and let them choose.
- **Play it back** in their words before you write it down, and let them correct it.
- **Write as you go.** The note is the record: each settled part goes into it straight away, so a fresh session can pick it up mid-conversation.
- **Say one short line before each step,** such as "Looking at what Channel Performance Weekly has today."
- **Never fill a template.** A section the conversation hasn't earned stays out. Never invent a metric, definition, object or name; ground each one in a file, a query result or the person's own words.
- **Use their vocabulary,** as their about.md says they like answers: a business reader hears outcomes and screens, a technical reader hears objects and fields.

### Procedure

1. **Read the guides first:** vfs://guides/plans.md, then vfs://guides/brainstorm.md. Never plan from memory.
2. **Continue, or start.** `kater scratch list --json` lists the person's topics. If one already covers this goal, read it and pick up where it stopped. Otherwise start `brainstorm/<topic>.md` in their brainstorm folder, named for the goal: a working title and `Status: open, planning`.
3. **Understand the goal.** Learn who it's for, the decision or habit it serves, what will be true when it's done, how they'll know it worked, when they need it, and what's out of bounds. Useful moves when an answer stays vague:
   - "What would you look at on Monday morning that you can't today?"
   - "Picture it done. What's on the screen?"
   - "If this went wrong, what would the reason be?"
   - "What's the smallest version that would already help?"
   - "Who else reads it, and what do they need from it?"

   `checkpoint:` write the title (the desired state, in their words), the opening paragraph and `## Desired state`. Read them back.
4. **See what exists.** Read `connection_context.md` and the person's `about.md`, list their workspaces and the connection's workspaces, search the catalog, and read the workspaces, queries, dashboards and apps the goal touches. Fill `## What exists today` with a row for each need: what's there today, by path, and the gap. Where a figure matters, run the query that shows it rather than describing it.

   `checkpoint:` show the table and ask what's missing or wrong.
5. **Shape it together.** For each gap, offer reuse, change or new, with your pick. Record each answer in `## Decisions`, settled by them with the date, or proposed with your recommendation when they'd rather you choose. Put what nobody can answer yet in `## Open questions`, with a recommendation and the phase it blocks.

   `checkpoint:` read the decisions back as one line each.
6. **Cut the phases.** Follow [A phase anyone can run](vfs://guides/plans.md#a-phase-anyone-can-run). Start with the smallest slice that makes the core figure run end to end, then add one concern per phase. Write the Phases table and one brief per phase, then "How to run a phase" with the plan's workspace, its constraints, and every write outside the folder.
7. **Check it against the bar** below, and fix what fails before showing it.
8. **Walk them through it.** Give one line per phase: what it builds and how you'll know it worked. Ask what's missing or in the wrong order, and revise.
9. **Ask to run it.** Ask once: "Shall I run it? P1 to P3 write to Channel Performance Weekly; nothing else leaves your brainstorm folder." A yes goes into `## Decisions` as its own decision ("Approved to run", which phases, their name, the date), and the `Status:` line becomes `open, ready to run`.
   - When they want it run now, hand off to [run plan](vfs://guides/run-plan.md).
   - Otherwise end with one line, the note's path, and how to run it later: "Run `brainstorm/<topic>.md`", or one phase at a time, "Run phase P1 of …".

The plan is the topic's note, `brainstorm/<topic>.md`, in the shape vfs://guides/plans.md gives, with `Status: open, ready to run` once it's approved. Its drafts go in `brainstorm/<topic>/`.

### The bar

The plan is ready when every one of these holds:

- [ ] The title is the desired state, and the opening paragraph says who it's for and what will be true.
- [ ] Every phase's brief stands alone apart from the plan: its objects by full path or `${...}` ref, its guides by path, and its decisions by number.
- [ ] Every Done when is a tool's verdict or the person's yes.
- [ ] P1 is the smallest slice that runs end to end, and depends on nothing.
- [ ] Every write outside `brainstorm/` is named in its phase and in "Leaving the folder".
- [ ] Every choice only the person can make is a decision, or stops the phase that needs it.
- [ ] No open question blocks P1.
- [ ] No rows, credentials or reasoning are stored in the note.
