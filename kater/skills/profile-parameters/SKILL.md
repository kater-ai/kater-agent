---
name: profile-parameters
description: "Profile the data for candidate values of an analysis's parameter. A step of deep-analysis."
---

This skill is Kater's guide `vfs://guides/profile-parameters.md`.

# Profile analysis parameters

Run declared governed profiling contracts and turn their returned aggregates into bounded parameter candidates.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## Procedure

1. Read the ready `profile_query` node, the contract's declared parameters, and its expected returned aggregate fields. Confirm the profile contract is built and compiled in the selected workspace.
2. Run the exact contract with `kater run --select <workspace>/<name>`, passing declared parameters only, with `--inputs-file -` keyed by the query's name (its id also works). Record the run identity, returned fields, row count, truncation, and a bounded summary in `## Profiling`.
3. Interpret returned aggregate statistics to present normally three candidates: sensitive, balanced, and conservative. For each state expected finding volume, coverage, stability, and tradeoff using only returned fields.
4. If the contract does not return the statistics needed to support candidates, block the decision node and name the missing governed profile contract or field. Do not compute missing quantiles, ranks, joins, or totals in memory.

## Output

Write a candidate table with the parameter name, value or allowed range, supporting run and fields, expected tradeoff, and limitations. Mark the profile node complete only after the tool call and evidence are recorded.
