---
name: run-analysis-dag
description: "Run the ready nodes of an analysis's DAG and record their evidence. A step of deep-analysis."
---

This skill is Kater's guide `vfs://guides/run-analysis-dag.md`.

# Run an analysis DAG

Execute ready analysis nodes in dependency order and write bounded execution provenance.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## Procedure

1. Read the DAG, object-status table, parameter decision, workspace resolution, and current node statuses. Run only ready nodes whose dependencies are complete and conditions are met.
2. For an `analysis_query`, invoke the named selected-workspace query contract with declared parameters. For an `insight`, invoke the named compatible insight template over its exact contract result with explicit column bindings and selected invocation parameters.
3. For every tool call, append its exact object identity, invocation parameters, run identifier, returned fields, row count, truncation, status, and concise bounded evidence to `## Execution evidence`.
4. Mark a node complete only after its tool call succeeds and evidence is recorded. Failed, invalid, insufficient, or truncated result states are failures or caveats, never silently healthy nodes.
5. On a declared material-finding condition, record the bounded finding keys at its decision node and hand off only to the declared diagnostic skill. Otherwise proceed to verification.

## Boundaries

Never invoke undeclared contracts, invent parameter mappings, perform an in-memory transformation, or bypass a decision node. Keep returned data bounded in the artifact; link to run identity rather than pasting a raw table.
