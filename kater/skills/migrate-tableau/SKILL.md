---
name: migrate-tableau
description: "Migrate a Tableau dashboard. Use it when the person asks for: \"Migrate this Tableau dashboard\", a dashboard moved from Tableau with its numbers checked against Tableau's."
---

This skill is Kater's guide `vfs://guides/migrate-tableau.md`.

# Migrate a Tableau dashboard

How to move one Tableau dashboard into Kater and check its numbers, step by step, for the `migrate` skill. Kater has no migration command. You get everything from Tableau yourself, read the workbook yourself, build with the `kater` CLI, and check each tile yourself under the rules below. The person reads your report, so every verdict names the files it compared. Run this in the person's own terminal, in their local copy (`kater git clone`). Through the Kater MCP alone, say that and stop. Kater ships no migration scripts: write the fetch, the comparison and the report writer yourself in the capture, and do not ask for them in a note or a decision.

## Ask once, before you build

Follow the procedure of [migrate](vfs://guides/migrate.md). Its one stop is after the gap list: get the dashboard, read the workbook and "[Filters that Tableau applies later](vfs://guides/migrate-tableau-workbook.md#filters-that-tableau-applies-later)", write the gap list, then ask the person one question and end your turn. The question asks whether to go on, whether you may publish the new workspace once its dashboard is saved, and whether you may add each shared model field that the gap list names, such as a Top N's rank over all rows or a FIXED LOD's value. Never make a workspace, a query or a model change before the answer: a Top N or a FIXED LOD built in the workspace first is built again. When the person's request already answers all three, as a request that says yes to each up front does, do not ask: show the gap list in your reply and go on.

## The truth rules

1. Tableau's numbers come only from Tableau, in a file written to the disk unchanged: a REST response that a command writes (`curl -o FILE`), or a file that Tableau downloaded. Never a number from a picture, a number that you type, or a Tableau MCP answer that you copy into a file.
2. Kater's numbers come only from `kater run --json`, written to a file as it answered.
3. Compare the two with a script that you write in the capture folder and that reads both files. Never compare numbers by eye in your reply.
4. Not checked never counts as a match.

## The steps

Read each part when you reach its step.

1. **Get it from Tableau.** Keep the capture, and get the workbook, each sheet's data and the pictures: [the capture](vfs://guides/migrate-tableau-capture.md).
2. **Read the workbook** and write the gap list: [read the workbook](vfs://guides/migrate-tableau-workbook.md). Then ask once, as above.
3. **Build** the model, the queries, the charts and the board: [the maps from Tableau to Kater](vfs://guides/migrate-tableau-maps.md).
4. **Check each tile and write the report:** [the check and the report](vfs://guides/migrate-tableau-check.md).
5. **Offer a feature request** for each gap that Kater cannot do yet, as "Feature requests" below says.

## Feature requests

For each gap that Kater cannot do yet, offer a feature request with `kater_send_feedback`, as [send feedback](vfs://guides/send-feedback.md) says, with `kind: feature_request`. Name the Tableau feature and what the sheet shows, such as "Tableau's scatter of CPC against spend by campaign". Never put a number, a value or a file of the capture in it.
