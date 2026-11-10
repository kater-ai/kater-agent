---
name: run-plan
description: "Run a plan's phases one at a time, checking each. Use it when the person asks: run my plan, carry on with a plan."
---

This skill is Kater's guide `vfs://guides/run-plan.md`.

# Run a plan

Run a plan's phases for the person, one at a time, until it's done, blocked, or needs them. They never have to know which agent builds what. The plan is a topic note in a brainstorm folder, in the shape vfs://guides/plans.md gives.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## Procedure

1. **Read the guide first:** vfs://guides/plans.md. Then read the whole plan.
2. **Check it's approved to run.** Decisions must hold the approval to run, naming the phases it covers. If it doesn't, or the next phase isn't covered:
   - Walk the person through the phases in a line each.
   - Ask once, naming every write outside `brainstorm/`.
   - On a yes, record it in Decisions as its own decision: which phases, their name, the date.
3. **Pick the next phase.**
   - An `In progress` phase comes first: it's a run that stopped.
   - Otherwise take the lowest ID that's `Not started` and whose phases in Depends on are all Done.
   - When none is runnable but some aren't Done, stop and report what blocks them.
4. **Run it as the Developer.** Say one line first, such as "Building P2, the ROAS dashboard." Then hand it to the developer sub-agent, or work as it when the host has none, and follow [run phase](vfs://guides/run-phase.md) from a fresh read of the plan.
5. **Check it as QA.** Hand it to the qa sub-agent, or work as it, and re-read the phase's Done when and its Notes. Each item needs a check's result there: the command and its verdict, or the person's yes.
   - **All there:** pass.
   - **Something missing:** the Developer gets one more try at that item.
   - **Still missing:** set the phase `Blocked (<what's missing>)`.
6. **Tell the person, in one line,** what that phase made and how it was checked, such as "P1 done: the weekly ROAS query runs, 5 weeks on each platform, the same as the draft." Then carry on with step 3.
7. **Stop and hand back** when:
   - a phase is Blocked;
   - a brief leaves a choice to the person;
   - something would leave the folder that the plan doesn't name;
   - anything would be scheduled or armed;
   - the person says to stop.

   Say what you need in one question, and leave the plan so a fresh run picks up from the table.
8. **Finish.** When every phase is Done, set the `Status:` line:
   - `applied, all N phases done`, when the plan changed real content;
   - `answered, …`, when it only found something out.

   Report in vfs://guides/brainstorm.md's Reporting shape: one line, the thing itself (the dashboard, app or page, with its link), what was left as is, and one natural next step.

## Rules

- **The plan is the state.** Read it again before every phase; never carry one phase's context into the next beyond what its Notes say.
- **Keep the person in the loop, not in the work.** One line per phase, and a question only where the plan says they decide.
- **Never change a brief.** A plan that's wrong goes back to the Planner, with the person, through [planning](vfs://guides/planning.md).
