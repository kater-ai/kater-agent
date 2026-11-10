---
name: run-phase
description: "Build one phase of a plan and prove its Done when. Use it when the person asks: run phase P2 of a plan."
---

This skill is Kater's guide `vfs://guides/run-phase.md`.

# Run a phase

Build one phase of a plan and prove it's done. The plan is a topic note in a brainstorm folder, in the shape vfs://guides/plans.md gives. The person asks with "Run phase P2 of `brainstorm/<topic>.md`", or it is handed over from [run plan](vfs://guides/run-plan.md), to the developer sub-agent when the host has one.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## Procedure

1. **Read the guide first:** vfs://guides/plans.md, "Running a phase".
2. **Read the whole plan fresh:** the title, `Status:` line, Phases table and its Notes, "How to run a phase", Desired state, Decisions, the phase's brief, then every guide and file its "Read first" names. Trust the table and Notes, not what an earlier phase remembers.
3. **Check it can start.**
   - Every phase in its Depends on is Done.
   - Its own Status isn't Done.
   - No open question it waits on is unanswered.
   - If the phase writes outside `brainstorm/`, Decisions holds the approval to run it.

   If any of these fails, change nothing and report exactly what blocks it.
4. **Mark it started:** the row's Status `In progress`, and the note's `Status: open, P2 running`.
5. **Build each bullet in order,** through the guide for each object and the typed operations vfs://guides/public-creation-workflow.md names. Draft and check a change to real content in `brainstorm/<topic>/` first, then apply it.
   - **Writes:** a write outside the folder that the phase names goes ahead under the approval. Any other write is a change to the plan: stop and ask.
   - **Choices:** where the brief leaves a choice to the person, stop and ask. Where it's yours, take the smallest one that meets the goal, and say so in Notes.
6. **Check every Done when** with the tool it names: `kater compile`, `kater run`, `kater compare`, the page at its address, or the person's yes. Fix what fails within the brief, and check again.
7. **Close it.**
   - **Done:** set Status `Done <date>`. Write Notes with what differs from the brief, each check with its command and verdict (row counts, never rows), and what's left open.
   - **Can't finish:** set Status `Blocked (<why>)`, keep what works, and write the exact next step in Notes.

   Then update the note's `Status:` line: `open, P2 done`, or `open, P2 blocked`.
8. **Report** one line, the evidence that it's done, and which phase is next.

## Rules

- **Stay inside the brief.** What its Out of scope names waits. A change the plan needs goes to the person as a proposal, never into the plan quietly.
- **Never call a phase Done without each check's result.** A draft that wasn't compiled and run isn't built.
- **Ask before scheduling or arming anything,** even in an approved plan.
- **Write nothing into the plan but its own row, Notes and `Status:` line.** The Planner owns the rest.
