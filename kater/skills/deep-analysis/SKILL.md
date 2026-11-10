---
name: deep-analysis
description: "Find out why a figure changed, or answer one complex question end to end. Use it when the person asks: why did it change, what drove it."
---

This skill is Kater's guide `vfs://guides/deep-analysis.md`.

# Deep analysis

**Goal:** Answer one complex business question within one resolved workspace: plan a typed DAG, build the reusable objects it lacks, profile and select its invocation parameters, run its bounded evidence and declared diagnostics, review it in a fresh context, and report what it found. The work leaves reusable workspace objects, never throwaway SQL.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

**Workspace boundary:** This skill resolves its workspace. Its first step selects and records one exact readable workspace for the question. Every later workspace operation reads that workspace; its query contracts stay inside it, and no step reads a contract from another workspace.

**Steps:** This is a skill with steps. Run it as vfs://guides/skill-steps.md says: read one step's guide at a time, record each step done in `skill-status.yaml` in the person's brainstorm folder, and save at each checkpoint. Each step names the sub-agent that does it.

## Steps

| Step | Guide | Sub-agent | Guides it uses | Produces |
| --- | --- | --- | --- | --- |
| 0 | [Resolve the workspace](vfs://guides/deep-analysis-resolve-workspace.md) | the host's own agent | resolve workspace | One exact, suitable workspace for this run |
| 1 | [Plan](vfs://guides/deep-analysis-plan.md) | planner | build plan, build analysis dag | Question, approach, typed DAG and object-status table |
| 2 | [Build](vfs://guides/deep-analysis-build.md) | developer | configure workspace, build query contract | Compiled objects in the selected workspace and bounded sample evidence |
| 3 | [Profile and calibrate](vfs://guides/deep-analysis-profile-and-calibrate.md) | analyst | profile parameters, select analysis parameters | Candidates backed by evidence, and selected provisional parameters |
| 4 | [Execute and diagnose](vfs://guides/deep-analysis-execute-and-diagnose.md) | analyst | run analysis dag, diagnose findings | Primary evidence and any declared diagnostic evidence |
| 5 | [Review](vfs://guides/deep-analysis-review.md) | qa | review analysis | Pass, caveats, or the exact blocker |
| 6 | [Report](vfs://guides/deep-analysis-report.md) | reporter | synthesize findings | Findings and natural next steps |

## Starting

1. Follow vfs://guides/skill-steps.md with this guide as the skill's main guide.
2. Choose the topic's slug from the person's question: lower snake case, at most five words, one that no note or folder in their brainstorm folder has yet. In a scheduled run, write down the choice and go on. Open the run's one note, `brainstorm/<slug>.md` in the person's brainstorm folder, with a `# Title` that says what the analysis answers, in the person's words, not their request, then `Status: open` (vfs://guides/brainstorm.md), and these sections: Request, Workspace resolution, Question, Approach, Analysis DAG, Object status, Profiling, Parameter decision, Execution evidence, Findings, Recommendations and Review. Start the run's entry in `skill-status.yaml` with `stepsCompleted: []`. The run's other files, when it has some, go in a folder beside the note, `brainstorm/<slug>/`.
3. Read the guide of step 0, [Resolve the workspace](vfs://guides/deep-analysis-resolve-workspace.md), completely, and do it. Plan, write, compile and run nothing before it records a valid workspace.
