---
name: build-query-contract
description: "Define a metric or a shared query step by step, then publish it. Use it when the person asks: define a metric, a query others read."
---

This skill is Kater's guide `vfs://guides/build-query-contract.md`.

# Build a query contract

**Goal:** One query contract, made by careful SQL development inside one workspace: find what the same workspace already has, choose an explicit strategy, prove the smallest slice that runs, then add one concern at a time with the evidence of a compile and a run.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

**Workspace:** This skill needs one selected workspace, the person's own and valid. Read its root `workspace.sql` before you look for anything. A connection's workspace can be read or changed as the person's grants allow.

**What you write:** Work as the developer sub-agent. You write query SQL only, and let the SQL writer write it wherever an operation expresses the change (vfs://guides/public-creation-workflow.md). SQL by hand is for what no operation expresses. The compiler builds model interfaces in memory from the SQL you write, and readers use the shared catalog.

**Steps:** This is a skill with steps. Run it as vfs://guides/skill-steps.md says: read one step's guide at a time, record each step done in `skill-status.yaml` in the person's brainstorm folder, and save at each checkpoint.

## Steps

| Step | Guide | Reads | Produces |
| --- | --- | --- | --- |
| 1 | [Discover](vfs://guides/build-query-contract-discover.md) | What the selected workspace has, and the readers that matter | The contract, the candidates' evidence, and one strategy |
| 2 | [Start the draft](vfs://guides/build-query-contract-start-draft.md) | vfs://guides/query-contract.md and the selected source's definitions | A query SQL draft and its first passing slice |
| 3 | [Iterate](vfs://guides/build-query-contract-iterate.md) | The draft, and only what the next check needs | A tested contract, built one small change at a time |
| 4 | [Verify and publish](vfs://guides/build-query-contract-verify-and-publish.md) | The draft, the readers it changes, and [the final checklist](vfs://guides/build-query-contract-checklist.md) | Published query SQL, or a draft that says what blocks it |

## Names in these guides

The steps name the run's places this way:

- **The contract's slug:** a lower snake case name for the contract, one that no note or folder in the person's brainstorm folder has yet. **The domain:** a lower-kebab-case name for the contract's group, such as `paid-media`.
- **The session log:** the run's note, `brainstorm/<slug>.md` in the person's brainstorm folder.
- **The copy (`COPY`):** a copy of the selected workspace in the topic's folder, `brainstorm/<slug>/workspaces/<workspace>`, where `<workspace>` is the selected workspace's folder name, which the copy keeps.
- **The draft (`DRAFT`):** `COPY/query_contracts/<domain>/<slug>.sql`, where its target will sit in the workspace. For `edit-existing`, it is the existing query's own file in the copy.
- **The target:** `WORKSPACE/query_contracts/<domain>/`, where `WORKSPACE` is the selected workspace's path.

## Session log and draft

Each run is its own topic in the person's brainstorm folder: its session log and, in the folder beside it, the copy, which holds the draft. Nothing outside the brainstorm folder reads either. A write there needs no yes; publishing to the workspace does.

- The draft is a real query in the copy. Step 2 makes the copy with `kater brainstorm copy WORKSPACE --topic <slug>`. The draft reads the copy's root and queries by the names the workspace uses, so its text moves to its target as it is.
- A save never fails: a draft that doesn't compile is saved, and the save's `errors` say what's wrong. Let the writer write it where an operation expresses the change: `kater model edit MODEL --path DRAFT … --write`, as vfs://guides/public-creation-workflow.md says. Write it by hand, with `write_file` or `edit_file`, only for what no operation expresses.
- The session log records the contract, the candidates, the selected strategy, the stage that passes now, short evidence of each experiment, and the target's path. It's a note with no frontmatter: it opens with a `# Title` naming the contract, then its `Status:` line (vfs://guides/brainstorm.md).
- The run's state is its entry in `skill-status.yaml`: `stepsCompleted`, `state`, `strategy`, `targetPath`, `currentPassingStage`, `nextAction` and, when blocked, `blockedOn`.
- Generated SQL, model interfaces in memory and raw query results are the compiler's or the runtime's output. Never write them in the copy or paste them in the session log.
- Keep one current draft. The log records only the hypothesis, the change, the bounded check, a summary of the result and whether to keep or change it.

## What the draft needs

The copy compiles and runs like any workspace, so the draft compiles and runs as it will at its target: `kater compile --select COPY` reports each model's outcome and diagnostics, and `kater run --select DRAFT` runs the draft with its declared test inputs. Neither publishes anything outside the brainstorm folder. `kater compare` says whether two answers are the same, such as the draft's and the published query's.

When an operation you need is not there, set the run's `state` to `blocked`, name the operation, keep the draft, and stop before you publish. Never work around it by writing into `modeling/`, generating raw SQL, or writing generated files.

## Starting

1. Follow vfs://guides/skill-steps.md with this guide as the skill's main guide.
2. Require a valid selected workspace of the person's own. When `skill-status.yaml` already has a `build-query-contract` run in flight, reuse its `runDate`, `contractSlug`, `contractDomain`, its session log, copy, draft and target, the selected workspace and the calling skill's record. Otherwise set the run's date to today, `YYYY-MM-DD` in the person's time, choose the contract's slug and domain, and record the paths and the calling skill's record before you read step 1.
3. Open the session log with a `# Title` naming the contract, then `Status: open`. Start the run's entry in `skill-status.yaml` with `stepsCompleted: []`, `state: discovering`, `runDate`, `contractSlug`, `contractDomain`, `workspacePath`, `strategy: undecided`, `targetPath: undecided`, `currentPassingStage: none`, `nextAction: discover`, and the calling skill's record when another skill started it.
4. Read the guide of [step 1](vfs://guides/build-query-contract-discover.md) completely, and do it. Read no later step's guide.

## How a run ends

- `built`: the query SQL is published to its path in the workspace with `kater workspace apply COPY --files DRAFT`, the selected models compile with their diagnostics reviewed, bounded runs of the final query and of the readers it changes in the same workspace pass, and `kater compare` says the published query gives the same rows as the draft.
- `blocked`: the draft in the copy and the exact next action are kept, but a missing capability, a missing definition, an ambiguity nobody resolved or a failed test stops a safe publish.
- Never call a draft `built` that parsed but has not compiled and returned representative rows.
- The session log's status line follows the state: `Status: applied` once `built`, and `Status: open` while drafting or `blocked`, with what blocks it after the word.

## Making objects

Read vfs://guides/kater-cli-commands-masterlist.md through the Kater MCP (a local copy has no guides) and the CLI's help. Make a workspace with `kater new workspace PARENT/NAME --from-model KIND.NAME`, PARENT the folder of workspaces it goes in, a query with `kater new query WORKSPACE/NAME --query-file QUERY.sql`, and a chart with `kater new chart WORKSPACE/NAME --source-query QUERY --definition-file PROPOSAL.json`, with `--kater-root` and the exact, complete workspace scope. These commands share the public options to scaffold, draft and create, the revision checks and the editor-aware apply. A query's file holds frontmatter and exactly one named query; a chart's proposal uses the current `chart.plan` request. Keep the identities and the save outcomes they return, and never try a create again after a navigation fails. An app is [build app ui](vfs://guides/build-app-ui.md)'s.
