---
name: calibrate-analysis-parameters
description: "Recommend a change to a reviewed analysis's threshold or sensitivity. Use it when the person asks: is my threshold still right."
---

This skill is Kater's guide `vfs://guides/calibrate-analysis-parameters.md`.

# Calibrate analysis parameters

Profile a reviewed analysis again and recommend a change to one of its invocation parameters, never applying it. Use this only after a [deep analysis](vfs://guides/deep-analysis.md) that a person watched has a reviewed note and its approved profiling contracts. It makes no workspace threshold and changes no analysis.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

**Steps:** This is a skill with steps that needs one selected workspace: the analysis's. Run it as vfs://guides/skill-steps.md says, one step's guide at a time.

## Steps

| Step | Guide | Sub-agent | Guides it uses | Produces |
| --- | --- | --- | --- | --- |
| 1 | [Profile again](vfs://guides/calibrate-analysis-parameters-reprofile.md) | analyst | profile parameters | Current bounded candidate evidence |
| 2 | [Recommend](vfs://guides/calibrate-analysis-parameters-recommend.md) | reporter, then qa | synthesize findings, review analysis | A recommendation, approved or blocked, that changes nothing |

## Rules

- Before you run anything, name the analysis, its selected workspace, its exact profiling contracts, the current parameter, the objective and the feedback there is.
- Each calibration is its own topic. Choose its slug in lower snake case, one that no note or folder in the person's brainstorm folder has yet, and open its note, `brainstorm/<slug>.md`, with a `# Title` naming what it calibrates, then `Status: open` (vfs://guides/brainstorm.md). Start the run's entry in `skill-status.yaml` in the person's brainstorm folder with `stepsCompleted: []`. Then read the guide of [step 1](vfs://guides/calibrate-analysis-parameters-reprofile.md).
- With no labelled outcome and no reviewer's feedback, optimize only a stated measurable proxy, such as how many findings, the coverage or how stable it is. Never claim it is more accurate.
- A recommendation changes nothing. Applying it to a later analysis is an operation of its own, with its own approval, which records where its parameter came from.
