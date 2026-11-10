---
name: resolve-workspace
description: "Pick the one workspace that fits a request, or start the person's own. Use it when the person asks: start a workspace, where should this go."
---

This skill is Kater's guide `vfs://guides/resolve-workspace.md`.

# Resolve a workspace

**Goal:** Select one exact workspace for this request, or make one of the person's own when they asked for it, without showing another person's own work and without keeping the choice anywhere but this run.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

**When another skill starts it,** such as [deep analysis](vfs://guides/deep-analysis.md), write the resolution in that skill's note, and make no second note. **When the person starts it,** such as with `/resolve-workspace` to start a workspace, the run has its own topic's note, `brainstorm/<slug>.md` in the person's brainstorm folder, which opens with a `# Title` and `Status: open`, and is set to `Status: answered` once the workspace is resolved (vfs://guides/brainstorm.md).

**Steps:** This is a skill with steps. Run it as vfs://guides/skill-steps.md says: read one step's guide at a time, and record each step done in `skill-status.yaml` in the person's brainstorm folder.

## Steps

| Step | Guide | Sub-agent | Guides it uses | Produces |
| --- | --- | --- | --- | --- |
| 1 | [Inventory](vfs://guides/resolve-workspace-inventory.md) | the host's own agent | the rules below | A bounded list of the workspaces the person can read |
| 2 | [Evaluate](vfs://guides/resolve-workspace-evaluate.md) | the host's own agent | the rules below | Each one ranked by how well it fits |
| 3 | [Select or create](vfs://guides/resolve-workspace-select-or-create.md) | the host's own agent, the developer when it makes one | the rules below, configure workspace | The selected workspace, or a new one of the person's own |
| 4 | [Verify](vfs://guides/resolve-workspace-verify.md) | the host's own agent | the rules below | The selected workspace, recorded for this run |

## Starting

1. Follow vfs://guides/skill-steps.md with this guide as the skill's main guide.
2. Write down the request, its source references, the fields it needs, what it means, whether it needs a compatible contract, and whether it needs to write. When the person started it, choose the topic's slug in lower snake case, one that no note or folder in their brainstorm folder has yet.
3. When another skill started it, record that skill's note and what to do on return in `skill-status.yaml`. Read the guide of [step 1](vfs://guides/resolve-workspace-inventory.md).

## Rules every step follows

1. Start an audit thread, and read the request's source references, its business intent, the fields it needs, the compatible contracts, and whether it needs writes.
2. Use `list_accessible_workspaces` to read only the person's own readable workspaces and the connection's readable workspaces. Read bounded metadata only: identity, path, whether it is the person's or the connection's, whether it is writable, one typed selected_source identity, its contracts and its catalog revision. Never list another person's own workspace.
3. Classify each candidate as `exact-fit`, `extendable-fit`, `read-pattern-only` or `not-fit`. Score, in order: whether its sources and fields have what the request needs, whether it means the same, whether its contracts can be reused, whether it can take the objects that are missing, then whether this session already uses it.
4. Select the clear best candidate and record why. When two candidates would change the meaning, the access or where the work stays, stop with one short question to the person. A workspace that is only a useful pattern to read is never where the work runs.
5. When no candidate fits, make a workspace of the person's own only when the request asks for one. Give it a lower-kebab-case name that no workspace has, select the shared semantic model it needs, and compile it with `kater compile --select WORKSPACE_PATH` before you go on. Never copy or make again a semantic model only to make a workspace.
6. Write `## Workspace resolution` in the calling note, and record the selected workspace, why, whether it is the person's or the connection's, when it was resolved and its revision in the run's entry in `skill-status.yaml`. Write the choice nowhere else.

## What the note holds

The calling note records the candidates as bounded identities and classifications, the selected workspace's exact `${workspace:...}` reference and path, the reason, whether it is the person's or the connection's, when, its revision, the person's answer to a tie, and whether a workspace was made. It holds nothing about another person's own workspaces.
