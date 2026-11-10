---
name: build-plan
description: "Turn one question into an analysis plan for one workspace. A step of deep-analysis."
---

This skill is Kater's guide `vfs://guides/build-plan.md`.

# Plan an analysis

Turn a confirmed business question into an executable, resumable analysis plan for one resolved workspace. Procedure knowledge lives in the guide; this file defines the planning contract.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

Use this when a question needs more than running one existing query contract and may require a workspace definition or contract change.

## Procedure

1. **Read the guide first:** vfs://guides/planning.md. Never plan from memory.
2. **Confirm the workspace boundary.** Require the selected workspace, record its exact path and personal or connection status, then read its root `workspace.sql`.
3. **Inventory before inventing.** Search connection-wide `modeling/` for datasets, relationships, shared semantic and Kater dataset models, and insight implementations. Search the selected workspace's semantic interface and query contracts. Inspect other readable workspaces for reusable definitions where model resolution and access allow it.
4. **Draft the approach.** State the question, evidence needed, assumptions, the candidate insight contract, and each bounded stop condition. Identify whether each requirement is connection-wide modeling, the workspace root interface, a same-workspace query contract, or a run-only invocation parameter.
5. **Scaffold what is missing.** First decide whether the existing workspace root declaration or a query contract can change. For a missing reusable field, metadata item, parameter, or filter, add a workspace-root row. For a missing executable analysis, add a same-workspace query-contract row. For a missing connection-wide graph capability, add a connection-modeling row. Never create a reusable definition solely to store an analysis threshold.
6. **Build the DAG.** Invoke [build analysis dag](vfs://guides/build-analysis-dag.md). Include governed profiling and a decision node for every unknown parameter. Include a diagnostic branch only when its condition, bounded finding keys, and exact follow-up contract parameters are known before execution.

## Output contract

Append these sections to the calling artifact:

- `## Workspace resolution` — exact `${workspace:...}` ref, path, personal or connection status, typed selected_source identity and workspace model name, and required interface definitions.
- `## Question` — the confirmed business question, grounded source references, constraints, and non-goals.
- `## Approach` — ordered steps with stated assumptions.
- `## Analysis DAG` — typed, dependency-ordered nodes using the canonical table below.
- `## Object status` — one row per object the plan touches.

| Object | Type | Status | Notes |
| --- | --- | --- | --- |
| `${query:example}` | query_contract | exists / needs-change / scaffolded | requirements or required change |

The status table is the Developer handoff. It builds every `scaffolded` or `needs-change` row and flips it to `built` only after the required checks pass.

| Node | Type | Depends on | Governed operation | Parameters | Run when | Produces | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `profile-peer-distribution` | profile_query | — | `${query:peer-distribution}` | declared date range | always | bounded distribution and coverage statistics | planned |
| `choose-sensitivity` | decision | profile-peer-distribution | analysis checkpoint | sensitive, balanced, conservative candidates | no value supplied | selected provisional parameter | planned |
| `detect-outliers` | insight | choose-sensitivity | `${insight_template:outlier_detection}` over `${query:outlier-analysis}` | declared method and sensitivity | parameter selected | bounded findings | planned |
| `diagnose-outliers` | diagnostic_query | detect-outliers | `${query:outlier-diagnosis}` | approved finding keys | material findings exist | driver evidence | planned |

Only `profile_query`, `decision`, `build`, `analysis_query`, `insight`, `diagnostic_query`, `verification`, and `report` are valid types. Every node must name an existing governed object or an `Object status` row; prose such as “run another query if useful” is not a node.

When the question compares periods, record the intended calendar axis, period meaning, output mode and date coverage in the query-contract row. Follow vfs://guides/period-comparisons.md: discover the receiving query's eligible typed operation, preserve missing-period and zero-baseline behavior, and use row windows only for genuinely row-ordered questions. Shared-model promotion is deferred; a comparison query does not authorize or require it.
