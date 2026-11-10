---
name: build-analysis-dag
description: "Turn an approved analysis approach into its DAG of queries and decisions. A step of deep-analysis."
---

This skill is Kater's guide `vfs://guides/build-analysis-dag.md`.

# Build an analysis DAG

Translate a confirmed analysis question into a typed, bounded, resumable DAG.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## Procedure

1. Give every node a stable lower-kebab-case identifier and one allowed type: `profile_query`, `decision`, `build`, `analysis_query`, `insight`, `diagnostic_query`, `verification`, or `report`.
2. Name an exact governed object or an `Object status` row for every operation. List explicit dependency identifiers, only declared invocation parameters, a run condition, a bounded output, and initial `planned` status.
3. Add a `profile_query` and dependent `decision` for each unknown parameter. Its decision options must be evidence-backed candidate values, not a hidden default.
4. Add a diagnostic branch only when its upstream material-finding condition, bounded finding keys, exact contract, and parameter mapping are declared. Do not route arbitrary insight output into an operation.
5. Add final `verification` and `report` nodes. The report depends on QA disposition and must name the evidence sections it uses.

## Output

Append the canonical `## Analysis DAG` table to the analysis note. The table is the execution contract; the Analyst may not add a node solely because a result seems interesting.
