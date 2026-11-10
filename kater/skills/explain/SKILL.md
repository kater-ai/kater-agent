---
name: explain
description: "How a metric, or a number on screen, is computed, in plain words. Use it when the person asks: explain this metric, how is this calculated."
---

This skill is Kater's guide `vfs://guides/explain.md`.

# Explain a metric

Say how a metric is computed, in the person's words, from its definition. Explaining reads; it runs nothing new and changes nothing.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

Use this when the person asks what a number means, how it's calculated, or why two that sound alike differ. To prove a figure against its source, use [check](vfs://guides/check.md).

## Procedure

1. **Find it.**
   - **A metric by name:** `search_catalog` for its model and field. When two could be it, ask which.
   - **A number on screen:** `kater here` names the tabs the person has open and each chart's and table's answer, and `kater answer ID` reads the rows they see, with the query and inputs behind them.
2. **Read its definition** with `explain_field`: its namespace, model and field, and for a number on screen the run's `result_id`, so it's explained as that run produced it. Read its breadcrumbs as given: what it starts from, then each step, including the source's steps in the layer that made them. A SQL mark holds the whole SQL, and an Excel formula stays its formula: one formula fills its column, and a formula that reads another row reads it in the query's order, over only the rows the query returns, so say the order and any row limit when they change the number. To read what its model or query is made of, use `kater model show REF`; for a number on screen, add `--result ID` to read that run's inputs, filters with their values and dates, rows and warehouse SQL, as vfs://guides/overview.md describes.
3. **Explain it,** in this order:
   - **What it measures,** in one sentence.
   - **How it's computed:** in words first, then the formula. Show the Kater SQL only when their About you says they like SQL.
   - **What it counts and leaves out:** its filters and exclusions, and for a number on screen the inputs it ran with.
   - **Where it's right:** the grain it's computed at, and anything it can't be added across.
   - **Where it comes from:** its model, and the warehouse table under it.
   - **Whose it is:** verified, from the shared models, or computed in a workspace or a query, and so unverified.
4. **What reads it,** when they ask or before they change it: `kater references FIELD --relation MODEL`.
5. **Offer next:** `/check` to trace a figure to its source, or `/compare` to see it move.

## Stop conditions

- Nothing defines it: say so, name the nearest fields `search_catalog` found, and offer [build query contract](vfs://guides/build-query-contract.md) to define it.
