---
name: plans
description: "Plans. Use it when the person asks for: A goal that needs more than one change."
---

This skill is Kater's guide `vfs://guides/plans.md`.

# Plans

A plan is a topic note in your [brainstorm folder](vfs://guides/brainstorm.md) that gets to a goal in phases. Each phase is small enough that someone with no other context can build it from its brief and prove it's done. Write one when a goal takes more than one change, such as a workspace and its queries, a dashboard over them, then an app. One analytical question is [deep analysis](vfs://guides/deep-analysis.md) instead, and one query is [build query contract](vfs://guides/build-query-contract.md).

Three skills do the work:

- **Write it with the person:** [planning](vfs://guides/planning.md), which the Planner agent uses.
- **Run one phase:** [run phase](vfs://guides/run-phase.md), which the Developer agent uses.
- **Run them all:** [run plan](vfs://guides/run-plan.md), so the person never has to.

## Where it lives

`brainstorm/<topic>.md`, named for the goal in lower snake case, such as `brainstorm/region_weekly_sales.md`. Its drafts, checks and pages go in `brainstorm/<topic>/`, as for any topic.

## The shape

The sections come in this order. Leave out a section that would be empty, apart from the Phases.

An illustration: `<workspace>` and the field names stand in for real ones.

````markdown
# Region managers see last week's sales before Monday's call

Status: open, planning

The five region managers want to see last week's net sales for their own region, against the four weeks before it, before the Monday sales call. When this is done, a dashboard in `<workspace>` shows that, filtered to one region, and a Monday report app links to it.

## Phases

Run one phase: "Run phase P1 of `connections/<connection>/users/<email>/brainstorm/region_weekly_sales.md`". Run them all: "Run `…/brainstorm/region_weekly_sales.md`". Each phase follows `vfs://guides/run-phase.md`. A phase starts once every phase it depends on is Done.

| ID | Phase | Depends on | Status | Notes |
| --- | --- | --- | --- | --- |
| P1 | Weekly sales by region | — | Not started | |
| P2 | Region dashboard | P1 | Not started | |
| P3 | Monday report app | P2 | Not started | |

**Status** is Not started, In progress, Done (with the date) or Blocked (with why). **Notes** say what differs from the brief, what was checked and what was left open.

### How to run a phase

- **Read first:** this plan's Desired state and Decisions, the phase's brief, and every guide and file it names.
- **Workspace:** `connections/<connection>/users/<email>/workspaces/<workspace>`.
- **Leaving the folder:** P1 and P2 write to that workspace, and P3 creates an app beside it. Approved to run, including those writes (Decisions, 4). Anything else asks first.

### P1. Weekly sales by region

**Goal.** One saved query returns net sales by region for each of the last five weeks.

**Read first:** `vfs://guides/query-contract.md`, and the workspace's `workspace.sql`.

**Build:**

- Draft it as a scratch statement in `brainstorm/region_weekly_sales/checks.sql` that reads the workspace, and run it.
- Add it to the workspace with `kater add-to <that file> --statement 0 --no-chart --to workspace:<workspace>`.
- Net sales is the workspace's own measure (Decisions, 2). Weeks start on Monday (Decisions, 3).

**Done when:**

- `kater compile` on the workspace passes.
- `kater run --select <workspace>/weekly_sales_by_region` returns 5 weeks for each region.
- `kater compare` of the scratch statement and the saved query says `same`.

**Out of scope:** the chart and the region filter, which are P2.

### P2. Region dashboard

…

## Desired state

- **Each region manager:** opens one dashboard on Monday, picks their region, and sees last week beside the four before it.
- **The sales lead:** reads the same figures for every region in the Monday report.

## What exists today

| Need | Today | Gap |
| --- | --- | --- |
| Net sales by week | A net sales measure in `<workspace>` | No query by region and week |
| A region filter | None | P2 adds one |

## Decisions

1. **Where.** `<workspace>`, not a new workspace. *Settled by Dana, 2026-10-02.*
2. **Net sales.** The workspace's own measure, after returns. *Settled by Dana, 2026-10-02.*
3. **Weeks.** Monday to Sunday. *Proposed:* it matches the call.
4. **Approved to run.** P1 to P3, including the writes "Leaving the folder" names. *Dana, 2026-10-02.*

## Open questions

1. **Should the report email itself on Monday?** Recommendation: not yet. Scheduling needs its own yes, after P3.

## Later

- A watch that flags a region whose sales drop by a quarter week on week.
````

- **The title is the desired state,** in the person's words, never their request.
- **The `Status:` line** starts with `open`, `answered`, `applied` or `dropped`, so the Sandbox and `kater scratch list` read it, then says where the plan is: `open, planning`, `open, ready to run`, `open, P2 running`, `open, P2 blocked`, `applied, all 3 phases done`.
- **The opening paragraph** says who it's for, why, and what will be true when it's done.

## A phase anyone can run

Each brief stands alone apart from this plan. Someone given only "Run phase P2" and the plan must be able to build it.

- **One concern.** A phase ends in something you can check, such as a query that runs, a dashboard that opens or a page that draws. Five Build bullets is a lot.
- **Smallest working slice first.** P1 makes the core figure run end to end; later phases add to it. Never plan a dashboard over a query that doesn't exist yet in the same phase.
- **Name everything.** Every object by its full path or `${...}` ref, every guide by its path, and every decision by its number. Never "the ROAS query" or "the usual workspace".
- **Say how, not just what.** Name the guide for each kind of object, and the command where one is known, such as `kater add-to` or `kater compare`. Changes go through typed operations, as `public-creation-workflow.md` says.
- **Done when is a tool's verdict.** `kater compile` passes, `kater run` returns the rows the goal needs, `kater compare` says `same`, the page opens at its address, or the person said yes. Never "looks right" or "is reasonable".
- **Prove it in the folder first.** A change to real content is drafted and checked in `brainstorm/<topic>/`, then applied.
- **Name every write outside the folder,** in Build and in "Leaving the folder". Scheduling or arming anything is its own phase, and it always asks.
- **A choice only the person can make** is a Decision before the phases start, or the first bullet of the phase that needs it, which then stops and asks.
- **Out of scope** names what waits for a later phase, so nobody builds ahead.

## Decisions and approval

- **Settled** decisions name who settled them and when. **Proposed** ones carry the recommendation, and a phase that relies on one builds it.
- **Approval to run** is its own decision: which phases, and that it covers the writes "Leaving the folder" names. Until it's there, phases build only inside `brainstorm/`.
- **Open questions** never block P1. One that blocks a later phase says which.

## Running a phase

- **Read the plan again first,** whole, rather than trusting what an earlier phase remembered. Its table and Notes are the record.
- **Check it can start:** every phase it depends on is Done, and nothing it needs is an open question.
- **Mark it:** the row's Status `In progress` and the plan's `Status: open, P2 running`.
- **Build the bullets in order,** inside the brief. A change the plan needs is proposed to the person, never made quietly.
- **Check every Done when** with the tool it names, and record each verdict in Notes: the command, the verdict and how many rows, never the rows.
- **Close it:** `Done 2026-10-02` with Notes, or `Blocked (why)` with what works kept and the exact next step.
- **Report** one line, the evidence, and the next phase.

When every phase is Done, the `Status:` line says so (`applied, all 3 phases done`), and the report follows [brainstorm.md](vfs://guides/brainstorm.md)'s Reporting.
