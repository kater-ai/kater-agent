---
name: model-table
description: "Model synced warehouse tables as shared models with first queries. Use it when the person asks: model a new table, a new connection's models."
---

This skill is Kater's guide `vfs://guides/model-table.md`.

# Model a table

Turn synced warehouse tables into shared semantic models and a few queries that answer real questions. Shared models are the data team's, so this needs edit access to the connection's `modeling/`, which every Developer and Admin has. When a write there is refused, an Admin took the person's access away: say that an Admin can give it back. Work with the person one step at a time.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

Use this when an admin has just connected a warehouse and synced its schemas (`kater init` hands over to it), or when a table nobody models yet should be. For a model someone already made, change it through its guide instead.

## Procedure

Read a model's parts with `kater model show REF`: a `db_dataset` has its warehouse table and columns, a Kater dataset its source and fields, and a semantic model its sources, relationships, required filters and fields. Each field's definition includes its breadcrumbs from the SQL. A field read as its source made it keeps that source's steps; hand-written SQL stays SQL. Use these cards to inspect the model before changing it, without running its rows. A DB dataset's key is `db_dataset.` and the name its frontmatter declares. When two connections have a model of that key, add `--connection NAME`.

1. **Learn the business.** Read the root `about.md`, `connections/<connection>/connection_context.md` and the person's `about.md`. An admin answers the root `about.md`'s questions in `kater init`, so never ask again for anything it already says, however short the answer. Ask only for a part it has no words for (what the company does, who uses its data, or the three to five questions or metrics that matter most), and record the answer there.
2. **Survey the data.** `connections/<connection>/modeling/db_objects/datasets/` holds one DB dataset per synced table, and `modeling/kater_objects/datasets/` one Kater dataset for each. Find the tables no semantic model reads yet, or the one they named. Say which look like facts (events, orders, payments) and which like dimensions (customers, products, dates), and which of them the questions depend on. Ask about anything names and columns don't tell you.
3. **Propose one to three semantic models.** For each: its grain, the Kater datasets it reads, its joins, and the measures and dimensions that answer the questions. Use the business's own metric definitions; where there's none, propose one and say it's unverified. Write the proposal in `brainstorm/model_<table>.md`, with its title and `Status: open`, and wait for approval.
4. **Build them.** Through the Kater MCP, write each model's whole file with `kater source write connections/<connection>/modeling/kater_objects/semantic_models/NAME.sql --text-file -` through `kater_apply`; in a person's own terminal, `kater new semantic-model` writes it. Follow vfs://guides/semantic-model.md and the guides it points to rather than restating them here. Compile until there are no errors. Each field you declare waits for a person's review before it shows as Verified: name them to the person, who reviews them in the model's page or with `kater review`. Never review a field yourself.
5. **Write a few queries.** Make a workspace over each new model (`kater new workspace NAME --connection CONNECTION --from-model semantic.NAME`), and for each question write a query there, compile it and run it. Show the first rows and check them with the person: a number nobody recognises is a modelling bug until shown otherwise.
6. **Save the work.** Through the Kater MCP, each change is saved as it's made. In a local copy, commit the new files and publish them with `kater git push -m "..."`, saying what was modelled; if the push reports conflicts, resolve them, commit, and push again. Set the note's `Status: applied`.
7. **Point to what's next:** `/test-model` to lock in the answers they checked, and `/tour` to show the team what's new.

Never invent a figure, column or definition. Report failures plainly, with the compiler's or the warehouse's message.

## Dataset keys

Read vfs://guides/dataset__kater.md before deciding a field is a key. Only a DB dataset or Kater dataset, including custom SQL, may write `-- @primary_key` or `-- @foreign_key`, with no argument and at most one per field. A primary key means one row per value and no empty value. A foreign key can repeat. Keys reach downstream fields by plain reference, parentheses and casts; calculations drop them. The published contract names the key's kind and its declaring dataset. A field name or `@format id` alone is no key.

The DB dataset sync verifies primary keys and suggests foreign keys by name. Accept a supported suggestion with `kater sync --resource-type db_dataset --scope connections/<connection>/modeling --foreign-key orders.customer_id`; remove it with `--no-foreign-key`. A later sync keeps the choice. Use `--no-profile` when only accepting marks, so no rows are sampled. Do not write keys on a semantic model or in generated DDL. Read vfs://guides/relationship.md for the join's target and cardinality, and vfs://guides/filters.md for an ID filter.
