---
name: brainstorm
description: "Brainstorm folder. Use it when the person asks for: A question that needs more than one query."
---

This skill is Kater's guide `vfs://guides/brainstorm.md`.

# Brainstorm folder

Your brainstorm folder is where you try things before you ask. Write a plan, run checks and draft a page there without asking first. Then ask once, at the end, with the evidence in hand. Only after a yes does anything leave the folder.

It's `connections/<connection>/users/<your email>/brainstorm/`, and every file and `kater` result names yours in `brainstorm`, so use that one. Like the rest of your Personal folder, only you and whoever you share that folder with can read it.

## Rules

- **Free inside.** Create and change anything in `brainstorm/` without asking.
- **Ask to leave.** Everything outside it, such as a change to a workspace's query, a model change or a schedule, waits for the user's yes. Only the user's other own drafts, such as a new query in their own workspace, go without one ([kater-command](vfs://guides/kater-command.md)). Reading a real workspace changes nothing.
- **Delete only when asked,** your own topics included.
- **A local copy keeps its own folder.** In a local copy made by `kater git clone`, a write in `brainstorm/` stays in that copy. The portal does not show it, and `kater git push` does not send it, so the answer says so in `local_copy`: tell the person. To try a draft in the portal, select the chart's pencil there and paste the file's SQL, or open the Sandbox there for any other scratch file. `kater add-to FILE --statement 0` adds a statement to a dashboard, and `kater dashboard look COPY` sends a workspace copy. For any other file, make it again in Files in the portal.
- **Rows stay out of files.** A note names what to run, a file and statement or a workspace and query, and records what ran and its verdict. It never stores rows. The one exception is a spike's sample rows ([Sample rows](#sample-rows)), which are made up to show a shape and never copied from a run.

## Topics

Give each question its own note at the top of the folder, named for the question in lower snake case, such as `brainstorm/promote_roas.md`. It holds the goal, the plan, what ran and the report. It opens with a title, then its status:

```markdown
# Promote ROAS to a measure

Status: open
```

- **Status** is `open`, `answered`, `applied` or `dropped`, and a short clause may follow it, such as `Status: answered, the same on all 6 queries`. Keep it current. It's `dropped` when the user says no.
- **A topic either answers a question or ends in an apply.** An answer, with its evidence, sets `answered`. A change applied after a yes sets `applied`. When the answer is "yes, and here's how", carry on in the same topic.
- **A spike says so.** A topic that asks whether something can work, or what it would look like, and ends in an answer rather than an apply, is a spike. Its note says so under its Status line, each line its own paragraph:

  ```markdown
  # Would a flow chart of the shopper journey read well?

  Status: open

  Kind: spike
  ```

  Only a spike's pages draw sample rows. When a spike turns into a change, make it `Kind: change` and replace its sample rows with queries.
- **A goal that takes more than one change is a plan:** a topic in [plans.md](vfs://guides/plans.md)'s shape, with phases that each say what to build and how you'll know it's done, so any phase can be run on its own and the whole plan can be run for the user.
- **Say up front what answers it, and stop there.** If you run out of road first, report what's still open.
- **A folder only when it needs one.** Checks, copies and pages go in a folder of the same name beside the note, such as `brainstorm/promote_roas/checks.sql`. A topic with only a plan is just its note.
- **Pick a topic up again** from `kater scratch list --json`, whose `topics` give each one's note, folder, title and status.
- **A finding can outlive its topic.** A fact about the user goes in their `about.md`, under the heading it fits: Role, Projects I follow, How I like answers or Who I work with; write it with no question and tell them. A fact about the data belongs in `connection_context.md`, which is everyone's, so propose it with `kater propose`. [lessons.md](vfs://guides/lessons.md) says where each lesson goes.
- **Skills with steps use topics too.** A skill with steps, such as [deep analysis](vfs://guides/deep-analysis.md), writes each run's plan to `brainstorm/<slug>.md`, and any other files to `brainstorm/<slug>/`, as its steps say. It tracks the run in flight in `brainstorm/skill-status.yaml` ([skill steps](vfs://guides/skill-steps.md)).

## The loop

Plan, try, check, show, ask, apply. Say one short line before each step, so the user can follow along: "Writing the plan in your brainstorm folder."

1. **Plan.** Write the topic's note, `brainstorm/<topic>.md`: its title, `Status: open`, the question, what you'll try, and what will answer it, or for a goal, the plan [plans.md](vfs://guides/plans.md) shapes. When the user asked only for a plan, stop here: one line, the note's path, and the choices only the user can make.
2. **Try.** Make scratch statements and run them, copy a workspace and change the copy (Copies, below), or draft a page or an app ([Draft apps](#draft-apps)). Make every query and statement here as anywhere else, by [query authoring](vfs://guides/query-authoring.md): from picks, with SQL only when no pick makes the change. To make a field several queries compute one field on the workspace's root, such as ROAS, change the copy with `kater model promote`, never by hand ([Promoting a field](#promoting-a-field)).
3. **Check.** Run what answers the question. Record what ran and the verdicts in the note, not the rows. For a dashboard in a copy, look at the copy with `kater dashboard look COPY` and fix what it finds ([Look at it](vfs://guides/dashboard-look.md#look-at-it)).
4. **Show.** Report in the shape below.
5. **Ask.** One question: whether to apply it, and where.
6. **Apply.** Only after a yes, through the call that writes it: `kater workspace apply COPY` for a change tried in a copy ([Applying a copy](#applying-a-copy)), `kater app apply DRAFT` for a draft app ([Draft apps](#draft-apps)), or `kater add-to FILE --statement N --to workspace:WORKSPACE` for a statement that becomes a workspace query. Then compare again, and set the note's `Status:`.

## Checking

Two answers are the same only when `kater compare` says so. Never compare rows yourself.

```text
kater compare --left connections/<connection>/users/<email>/brainstorm/promote_roas/checks.sql#0 --right connections/<connection>/users/<email>/brainstorm/promote_roas/checks.sql#1
```

- **Sides.** Name each side by its complete path: a scratch statement as `FILE#N`, counting from 0, or a saved query as `<workspace>/<name>`, its workspace's folder and its name, never its file. With a workspace folder on both sides, their queries pair by name.
- **Verdicts.** Each pair is `same`, `different` with its first 50 differences, or `refused` with why, such as a side that doesn't compile. A refused pair doesn't stop the others. Across two workspaces, a query only one has is `only_left` or `only_right`, which makes the two different. Across a copy and the workspace it copies, `changed` marks each query the copy changed, and the card opens on one of them.
- **Rows** pair on the dimensions both sides return, or on `--key`; with none, they compare as multisets. `--column LEFT=RIGHT` pairs columns of different names.
- **Numbers** compare at their format's decimal places, as a stakeholder sees them. `--precision N` compares finer.
- **Inputs.** Each side runs at its defaults, with its own filters on. `--parameter NAME=VALUE` sets a parameter on every side that declares it.
- **Record the verdict** in the note, such as "weekly ROAS from the measure vs the inline ratio: same, 13 rows". Never the rows.
- **On screen.** In chat, a `kater compare` result draws itself: the verdict, each pair and the cells that differ. In the portal, a copy's own page draws it against the workspace it copies, from Compare in its banner, at `/workspace/<copy>?compare=open`.

## Reporting

One line, then the thing itself. Never a paragraph about it.

- **One line:** the answer, in the user's words.
- **The thing itself:** the run, card or page that shows it.
- **Same or different** is a tool's verdict, never your own reading of rows.
- **Left as is:** anything you didn't change, with the reason.
- **One question:** whether to apply it, and where.

## Scratch statements

A scratch file is any `.sql` file at any depth under `brainstorm/`: a worksheet of statements over shared models, one of your workspaces or the warehouse. Make each statement from picks, as the query-authoring skill makes a query: [Writing a statement](vfs://guides/sandbox-scratch-files.md#writing-a-statement) says how, and [Sandbox](vfs://guides/sandbox-running.md) says how to run one.

- **New ones go at the top,** as `unnamed_scratch_<n>.sql` (`kater new scratch`). A topic's checks go in the folder beside its note, such as `promote_roas/checks.sql` (`kater new scratch --folder promote_roas`).
- **A file's identity is its path under `brainstorm/`,** so two topics can each hold a `checks.sql`.
- **`<topic>/workspaces/` holds workspace copies** (Copies, below), whose `.sql` files are their models, not scratch files. **`<topic>/apps/` holds draft apps** ([Draft apps](#draft-apps)), which hold no SQL: a `.sql` file there is refused.
- **Run a statement** with `kater show --select FILE --statement N`. Saving never fails on a statement; the run reports its errors.

## Copies

A copy of a workspace is a real workspace in the topic's folder, `brainstorm/<topic>/workspaces/<name>/`, with its own `workspace.sql`, `query_contracts/` and `charts/`. Change it freely: the original is unchanged.

- **Make one with `kater brainstorm copy WORKSPACE --topic TOPIC`,** the workspace by its full path. Any workspace you can read in its connection can be copied, someone else's included. The copy keeps the original's name, so the two pair up in `kater compare`; `--name` gives it another. A name the topic already has is refused: work in that copy.
- **The answer** is the copy's path, how many files it copied and the errors of its one compile. A copy that doesn't compile is saved anyway, so fix it in place.
- **Show it with `kater brainstorm show COPY`,** the copy's full path. It says what the copy changed since it was copied or last applied, and whether each file would apply, without compiling. In chat it draws with Compare and Apply, which the person runs there.
- **`copied-from.yaml`** at the copy's root names the original and, for each file, its revision in the original and in the copy when the two last matched, which applying the copy reads. Leave it as it is.
- **The original's apps stay with it.**
- **Never for real work.** A copy is for trying a change. Answer the user's questions from their real workspace, and never choose a copy when you choose a workspace.
- **It works like any workspace.** `kater compile --select COPY`, `kater run --select FILE`, `kater compare --left ORIGINAL --right COPY` and every typed edit work on it, each named by its full path.
- **Make a query in it** by [query authoring](vfs://guides/query-authoring.md), with the copy's full path as the workspace: `kater query fields COPY`, then `kater query new COPY --request-file -`, with no `namespace` in the request: the command sends the copy's. Change it with `kater model edit`, as the skill says.
- **A save never fails.** A copy's file saves even when it doesn't compile, and the result's `errors` say what's wrong. A typed edit on a broken file refuses and says why.
- **A scratch statement reads it by its full address,** `workspace."connections/<connection>/users/<email>/brainstorm/<topic>/workspaces/<name>".<root name>`, where the root name is the copy's folder name, which its `workspace.sql`'s `name` repeats. `sandbox.statement.write` with the copy as its `workspace` writes it for you. `workspace.<name>` reads your real workspace or a connection workspace, never a copy.
- **It stays in the folder.** Workspace lists mark it `brainstorm`, and nothing offers it: not the Catalog, Explore, Add to or a workspace picker. An app outside `brainstorm/` never reads it; a page in your brainstorm folder may.
- **Open it** in the portal at `/workspace/<its full path>`, or from its files in the Sandbox with Open in app.

## Applying a copy

After the user's yes, `kater workspace apply COPY` writes the copy's changes back to the workspace it copies, which its `copied-from.yaml` names. Show what it would write first.

1. **Dry run.** `kater workspace apply COPY --dry-run`, with the copy's full path. Nothing is written. The answer lists each file the copy changed since it was copied, with its diff against the original and whether it would be written. A file only the original changed isn't listed: it stays as the original has it.
2. **Ask** with the diffs and the comparison's verdict in hand.
3. **Apply** with the dry run's `apply` line, through kater_apply. It names the dry run's `--revision`, so it writes exactly what the dry run showed, and it's refused with `proposal_changed` when the copy or the original changed since.
4. **Compare again.** `kater compare --left ORIGINAL --right COPY` should say every query the same. Then set the note's `Status: applied`.

- **Drift.** A file that changed in the original since the copy is refused with `origin_changed` and both revisions, and there's no force: writing it would undo someone else's change. Copy the workspace again under another name (`--name`), make the change there, and apply that copy. The copy's other files still apply.
- **The compile gate.** The copy's Kater SQL is compiled with the rest of the original first, as any save is. When a file doesn't compile there, nothing is written, and the answer names each file and its errors: fix it in the copy, compare again, then apply.
- **Deletes.** A file the copy deleted is deleted from the original only when you name it with `--files`, by its path in the copy. A query that a chart left in the original still reads isn't deleted.
- **Some files.** `--files FILE`, repeatable, applies only those. A dry run's diffs share one budget; a diff left out is read by naming its file.
- **Each file's outcome** is reported, and an apply isn't all or nothing: a refused file leaves the others written. Afterwards the copy matches the original again, so a second apply sends only what changes after it.
- **Who can apply.** Only someone who can write the original: the answer's `access` says whether you can.
- **Apps.** The answer's `apps` are the apps that read the original. After an apply, show one with `kater brainstorm show APP`, which says whether each of its tags still draws, before you report.

## Promoting a field

When several queries in a workspace compute the same thing, such as ROAS, `kater model promote` makes it one field on the workspace's root and points each of them at it. Try it in a copy first.

1. **Propose.** `kater model promote FIELD --relation QUERY --scope COPY --dry-run`, with the copy's full path. Nothing changes. The result names the root field, each place that reads it instead (its query, CTE, line, and SQL before and after), and each field it leaves as is, with why.
2. **Read the checks.** One compile of the copy proves that every query compiles, that each place keeps exactly its formula, and that nothing else changes. Its `result_check` runs each query the change reaches on the warehouse, as it is and as the change would leave it: Kater's worker runs it, and the command waits for it. Writing the change follows the same check.
3. **Write it** in the copy with the result's `apply` line, which names the dry run's `--revision`. It's refused if anything changed since the dry run.
4. **Compare** the copy with its original with the result's `compare` line. Every query should be the same.
5. **Report** one line, such as "ROAS can be one measure. Same numbers in all 6 queries.", then the card the result draws: the root field's definition, each place it replaces with Same numbers, and what's left as is. Then ask, and after a yes apply the copy to its original ([Applying a copy](#applying-a-copy)).

- **A place** reads the workspace's fields and computes the same formula, in a query's own SELECT or in one of its CTEs. A query's field that computes it from columns its CTEs pass on is a place too: the CTE that reads the workspace gains the root's field as a column, each CTE after it passes it on, and the field reads it. Sameness is the formula the compiler finds, never the SQL's text.
- **Left as is:** a field over the same inputs that computes something else, such as a 13-week ROAS over a window or a total of a CTE's weekly rows, and one that computes it from a CTE's columns, where the root's field can't be read.
- **The name** is the field's own, unless another field in the workspace has it, such as the field itself when its query keeps it; then it's the column the formula is computed in. `--name` picks another, and a name another field has is refused. `--label` sets its label.
- **Only the root of the same workspace.** A field that uses a window, a custom calculation or a parameter stays in its query. A shared model's field is the data team's change, not this one.
- **On a real workspace** the dry run works the same, but it has nothing to compare with, and writing it goes through kater_apply after a yes.

## Notes and pages

- **A note** is a `.md` file, such as a topic's `promote_roas.md`.
- **A page** is an `.html` file in the topic's folder, such as `brainstorm/journey_flow/pages/flow.html`. Write it as an app page ([apps](vfs://guides/app.md)) with no manifest: rows come only through Kater tags, and no figure is typed in. It wears Kater's default theme. A spike's page may also draw sample rows ([Sample rows](#sample-rows)).
- **A comparison on a page** is a `<kater-compare left="SIDE" right="SIDE">`, each SIDE a query as `<workspace>/<query>` (never its file), a scratch statement as `FILE#N` or a workspace folder, such as a query and the same query in your copy. It draws the verdict and the cells that differ, and compares again each time the page opens ([apps](vfs://guides/app-data.md#comparisons), Comparisons).
- **Tags name a query by its full path,** `query="connections/<connection>/users/<email>/workspaces/<workspace>/<query>"`: any query you can read in the page's connection. A page reads only the queries its own tags name.
- **Its address** in the portal is `/sandbox/brainstorm/<connection>/<email>/<path inside brainstorm/>`, such as `/sandbox/brainstorm/snowflake_prod_connection/yvonne@kater.ai/journey_flow/pages/flow.html`. A note opens there too, as Markdown. Only you, admins and people you share it with can open either, and every open copy reloads when you save the file.
- **Check a page before you report it.** `kater brainstorm show PATH`, with the page's full path, says whether each tag draws: its query exists, compiles and returns every field the tag names, and a chart along an axis gets one value for each point. A tag that can't says why, and nothing runs in the warehouse. Fix every tag that can't draw, then report.
- **Show it with `kater brainstorm show PATH`.** In a chat that draws apps, the page appears in the chat under its name, with Reload and Open in Kater, and a note as Markdown with its status; a scratch file shows each statement with Run, so the person can see its answer drawn. Elsewhere the result's `app_url` is its link. After you change the page, show it again.
- **An app the user already has shows the same way.** `kater brainstorm show` on an installed app's folder, or one of its pages, draws the app in the chat, as anyone it's shared with sees it. Show it before you rebuild what it already draws.
- **In VS Code,** **Kater: Open Page** (the **Open page** or **Open note** lens, or the editor title button) opens the file in a tab beside its text, drawn the same way for whoever is signed in to Kater, and reloads it on save. The Kater Sandbox draws a page in its page tab too. Signed out, the tab says so, with a **Sign in** button and the file's text.

## Draft apps

An app the user would keep, such as a deck or a report, is drafted in the topic's folder, `brainstorm/<topic>/apps/<name>/`, and shown there before it moves to their apps.

- **Its folder** holds `kater.app.yaml`, its pages, `index.html` first, and any styles or scripts they load. The manifest needs only its version, a name that matches the folder and a label, and may name a theme; moving it adds its owner:

  ```yaml
  version: kater.app-manifest/v3
  application:
    name: cmo_deck
    label: CMO Monday deck
    theme: spring
  ```

  Write its pages as any app's ([apps](vfs://guides/app.md)): a deck or a report names its template on `<html>`.
- **Its tags read real workspaces' queries by full path,** which changes nothing. A slide that needs a query the workspace doesn't have reads it from a copy in the same topic, where you make the query from picks ([Copies](#copies), Make a query in it).
- **Show it with `kater brainstorm show FOLDER`,** its folder by full path. In chat it draws in its own theme, with a picker for its other pages, and the result says whether each tag draws and whether it can move as it is. In the portal it opens at `/sandbox/brainstorm/<connection>/<email>/<topic>/apps/<name>/index.html`. Nothing outside your brainstorm folder lists, checks or builds a draft.
- **Move it after a yes.** `kater app apply FOLDER --dry-run` checks the move and writes nothing. Its `apply` line, through kater_apply, writes the app to `users/<email>/apps/<name>/` with you as its owner, and deletes the draft. `--name` gives it another name; a name your apps already have is refused. Then show it where it is with `kater brainstorm show users/<email>/apps/<name>`.
- **What stops a move,** each named with where it is: a tag that reads sample rows, so point it at a query first; a tag that reads a copy whose changes aren't applied yet, so apply the copy first, and the move points the tag at the original; a scratch statement or anything else in a brainstorm folder; a query that doesn't resolve or compile, or a tag that doesn't fit its query; and a file outside app-kit's boundaries, such as an import of Kater's own UI or an environment file. Nothing is written until every check passes.

## Sample rows

A spike's page can show a shape before the query behind it exists: a tag reads sample rows typed into the page in place of a query.

```html
<kater-chart kind="bar" sample="weekly" dimension="week" measures="spend" heading="Spend by week"></kater-chart>

<script type="application/json" id="weekly">
{
  "fields": [
    {"name": "week", "label": "Week", "data_type": "date"},
    {"name": "spend", "label": "Spend", "type": "measure", "format": "currency('USD', 0)"}
  ],
  "rows": [
    {"week": "2026-09-07", "spend": 12400},
    {"week": "2026-09-14", "spend": 13850}
  ]
}
</script>
```

- **Only in a spike.** A tag draws sample rows only on a page in the folder of a topic whose note says `Kind: spike`, a draft app's page there included. Anywhere else, an installed app included, it says it can't draw, and the app check refuses it, so a draft app moves only once every tag reads a query.
- **A tag reads its query or its sample rows, never both.** `sample="ID"` names a `<script type="application/json" id="ID">` block on the same page. A chart, table, KPI or value reads one block, and a frame may read several, separated by spaces. A filter reads none, since sample rows have no inputs.
- **A block** holds `fields` and `rows`, and may hold a `title`, which a card shows when its tag has no heading. Each field has a `name`, the way a tag names it, and may have a `label`, a `type` (`dimension`, the default, or `measure`, whose values are numbers), a `data_type` (`string`, `number`, `boolean`, `date` or `timestamp`, else its values decide) and a `format`, written as a `-- @format` is. Each row is an object of values by field name, at most 1,000 rows.
- **Made up, never copied.** Sample rows are the one place figures nobody ran appear, and only to show a shape: how a chart reads, how many categories fit. Type plausible values, never rows from a run, since rows stay out of files. Never quote a figure from them; what a spike reports is whether the shape works.
- **Every widget they feed says Sample data,** in a snapshot too, and the page can't be exported. `kater brainstorm show` names each tag's `sample`, says when the page draws sample rows, and says why one can't draw.
- **Once the query exists,** replace `sample="…"` with `query="…"` and delete the block. A change topic, and anything that leaves the brainstorm folder, shows real rows only.
