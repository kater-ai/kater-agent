---
name: select-analysis-parameters
description: "Record the choice of a profiled parameter at an analysis's decision node. A step of deep-analysis."
---

This skill is Kater's guide `vfs://guides/select-analysis-parameters.md`.

# Select analysis parameters

Record the person's decision, or a decision an authorized policy makes, for a profiled invocation parameter.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## Procedure

1. Confirm the dependent profiling node is complete and show the evidence-backed candidates, tradeoffs, and provisional nature in plain language.
2. In a run a person watches, suspend at the decision node until the user selects or adjusts a supported value. Do not restart the analysis when they respond.
3. In a scheduled run, which nobody answers, continue only when the artifact names the already-authorized selection policy and it covers the parameter. Record the selected value as provisional.
4. Validate that the selected value is declared by the query contract or insight template. Persist it, its source, policy or user decision, rationale, timestamp, and dependent node identifiers in `## Parameter decision`.

## Stop conditions

Block when the selection is absent, outside the supported contract surface, or, in a scheduled run, has no authorized policy. Never add a workspace threshold setting or silently select a parameter.
