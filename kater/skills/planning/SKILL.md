---
name: planning
description: "Workspace Planning Reference. Use it when the person asks for: A question that needs more than one query."
---

This skill is Kater's guide `vfs://guides/planning.md`.

# Workspace Planning Reference

## Purpose

Use this guide when a question needs more than one governed operation, a new or changed object, profiling of an unknown parameter, a conditional diagnostic, or future recurring execution. The result is one evolving artifact, a topic's note, `brainstorm/<topic>.md`, in the current user's [brainstorm folder](vfs://guides/brainstorm.md), never a second free-standing plan document. A goal that takes several changes, rather than one question, is a plan in phases instead: [plans.md](vfs://guides/plans.md).

## Preconditions

The Conductor resolves one exact readable workspace before planning. The Planner records its `${workspace:...}` ref, exact path, personal-or-connection status, one typed selected_source identity, catalog revision, reusable configuration, and contract inventory. A plan can inspect connection-wide modeling and readable connection workspaces for patterns; an execution DAG may reference only selected-workspace contracts and configuration.

## Planning sequence

1. Restate the business question, its governed source references, decision to support, constraints, and non-goals.
2. Inventory reusable connection-wide semantic models, selected-workspace definitions, query contracts, and compatible insight templates before proposing any new object.
3. Classify each missing item as connection-wide modeling, the reusable workspace root interface, a selected-workspace query contract, or a run-only invocation parameter. Threshold-like values are always invocation parameters.
4. State the approach in bounded operations with the evidence each operation contributes and the condition that stops further work.
5. Create one typed DAG. Every node has an exact governed operation, explicit dependencies, declared parameters, a run condition, a bounded output, and a status. Unknown parameters require a `profile_query` followed by `decision`; follow-up diagnostics require an explicit condition and bounded finding keys.
6. Create one object-status row for every object used or changed. The Developer owns the build rows; the Analyst does not author missing objects while executing the DAG.

## Required artifact sections

After its `# Title` and `Status:` line, the artifact contains these sections in this order: `## Request`, `## Workspace resolution`, `## Question`, `## Approach`, `## Analysis DAG`, `## Object status`, `## Profiling`, `## Parameter decision`, `## Execution evidence`, `## Findings`, `## Recommendations`, and `## Review`.

The artifact stores object references, request-scoped workspace selection evidence, parameters, run identifiers, bounded summaries, decisions, uncertainties, and validation outcomes. It never stores credentials, raw result dumps, hidden reasoning, or a computed transformation the governed query did not return.

## Analysis DAG

| Node | Type | Depends on | Governed operation | Parameters | Run when | Produces | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `profile-peer-distribution` | profile_query | — | `${query:peer-distribution}` | selected date range | always | distribution and coverage statistics | planned |
| `choose-sensitivity` | decision | profile-peer-distribution | analysis checkpoint | candidate values | no value supplied | selected provisional parameter | planned |
| `detect-outliers` | insight | choose-sensitivity | `${insight_template:outlier_detection}` over `${query:outlier-analysis}` | declared method and sensitivity | parameter selected | bounded findings | planned |
| `diagnose-outliers` | diagnostic_query | detect-outliers | `${query:outlier-diagnosis}` | approved finding keys | material findings exist | volume-versus-depth evidence | planned |

The valid types are `profile_query`, `decision`, `build`, `analysis_query`, `insight`, `diagnostic_query`, `verification`, and `report`. A node is complete only after the named tool succeeds and the artifact records its bounded evidence. Insight output cannot become arbitrary input to another operation; a diagnostic branch only receives the approved finding keys and declared parameters recorded at its decision checkpoint.

## Parameter calibration

Profile through governed query contracts. Use returned aggregate evidence to describe sensitive, balanced, and conservative candidates, including expected finding volume, coverage, stability, and tradeoff. Do not calculate missing quantiles, rankings, joins, or totals in memory. An interactive run stops at the decision node; an autonomous run needs an already-authorized policy and marks the selected value provisional. Store the selected value and rationale as run provenance. A later calibration may recommend a different parameter but never changes a running or scheduled value without approval.

## Object status

| Object | Type | Status | Owner | Evidence required | Notes |
| --- | --- | --- | --- | --- | --- |
| `${query:example}` | query_contract | exists / needs-change / scaffolded / built | developer | compiler validation and bounded sample run | requirements or required change |

An `exists` row names the current compatible identity. A `needs-change` or `scaffolded` row is not runnable until the Developer records a successful build. Connection-wide changes retain their privileged authoring path; do not use the deep_analysis skill to create a duplicate semantic model merely because a workspace needs to select it.

For every planned output, state whether it inherits a source identity, declares a new named measure, dimension or custom calculation, or changes only an AS label. Record complete model/workspace namespaces in dependencies and bindings. Name any unnamed output that another analytical model must consume, and review all external dependents when changing a declaration name.

## Recurring checks

- Goals, Targets and Triggers are not built: no code reads or runs them. Do not write these files. For a recurring check, use a workflow (vfs://guides/workflows.md).
- Recommend only bounded actions a named individual can perform after required approval.
- Recommend useful recurring checks, but make arming opt-in. Seed a new threshold from a measured baseline, describe it as provisional, and offer “flag for later.”
- Kater can send one saved query’s fresh figures to its owner in Slack. Follow [Schedule a Slack DM](vfs://guides/schedule.md), confirm the owner’s IANA time zone and connected Slack account, and save the file through `kater_apply` after approval. Writing an unpaused schedule arms it.
- A Slack schedule runs at each set time, including when its query returns no rows. It is not a conditional watch or a model-written digest. A watch, a workflow of kind `watch`, tells its owner only when a metric crosses its line, and a brief writes a digest each time (vfs://guides/workflows.md). A schedule file sends only to its owner. A dashboard's `schedule` sends to each person in `to` in Slack (vfs://guides/dashboard-file.md), and a snapshot workflow may post in one Slack channel. Email has no supported setup yet.
- Set delivery up only where the environment can arrange it. Unknown data cutoffs remain `null`; execution time does not establish data coverage. Do not report a saved draft, unavailable delivery setup or failed run as an active schedule.

## Object references (`${...}`)

- Write tracked objects as typed `${...}` refs whenever authoring or editing Markdown or governed YAML. Use `${workspace:name}`, `${semantic:name}`, `${kater_dataset:name}`, `${db_dataset:name}`, `${query:name}`, `${insight_template:name}` and `${doc:name}`: a model and a query take their own kind word. Kater refuses `${semantic_model:name}`, `${dataset:name}` and `${query_contract:name}`. `${workspace:name}` reads as SQL reads a workspace's name: in a file of your own folder, your own workspace of that name or the connection's; in any other file of the connection, the connection's. When you and the connection both have a workspace of that name, the name names neither, as SQL refuses it: name it by its path, in a mention, as you name someone else's workspace. Write a field as `${model.field}`, a named workspace field as `${field_name}`, and a method as `${field.METHOD()}`. Write each reference outside backticks: a reference in a code span or a code block is only text, an example that names nothing, so no reader finds it, a rename leaves it as written and Kater never refuses it.
- Each object's declared `name` is its identity, not its file path. Do not wrap warehouse paths, literal values, unknown columns, file paths, or question IDs.
