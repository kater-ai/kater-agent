---
name: digest
description: "A brief of the person's metrics on a schedule, and the digest page's template. Use it when the person asks for: a weekly digest, a Monday brief."
---

This skill is Kater's guide `vfs://guides/digest.md`.

## Digest template

A digest is an app page laid out as a brief someone reads first thing, such as a Monday brief of what moved in the metrics they watch. It reads in one column, insight first: the one thing that matters most, the week in figures, what moved, what held steady, and what to do this week. The page names the template on its root, and the runtime links the template's stylesheet before the page paints:

```html
<html lang="en" kater-template="digest">
```

The template is a stylesheet and a script the runtime serves, never a copy in the app folder, so a fix reaches every brief. It uses only the theme's tokens and follows the host's light or dark mode. It reads best in the `journal` theme: set `application.theme` to `journal` in `kater.app.yaml`.

## Set up a recurring digest

A digest is [brief me](vfs://guides/brief-me.md) on a schedule. When the person asks for their metrics on a schedule, such as `/digest` or "every Monday", draft it in their brainstorm folder, show one issue, then ask. Nothing is scheduled, armed or sent without their yes. For one brief now, use brief me. To hear only when a figure moves, set an alert on it ([pin and alerts](vfs://guides/pin-and-alert.md)), or use [watch](vfs://guides/watch.md) for a metric that no chart draws.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

1. **Open a topic,** `brainstorm/weekly_digest.md` in the person's brainstorm folder, or named for its cadence, with its title and `Status: open`.
2. **Settle what it covers:** the metrics in their About you, each with a verified definition as brief me finds it; the window, such as the last full week against the four before it; when it comes; and where it goes. Ask only for what you can't infer.
3. **Draft one issue:** an app in the topic's folder, `brainstorm/weekly_digest/apps/monday_brief/`, laid out by this template with `theme: journal`: the one thing first, the week in figures, what moved, what held steady and this week's steps. Its queries go in a Monday Brief workspace of their own, `connections/<connection>/users/<email>/workspaces/monday_brief/`, as [Writing the queries](#writing-the-queries) says. No figure is typed in. Check it with `kater brainstorm show PATH` until every tag draws, then show it. After a yes, `kater app apply` moves it to their apps when they want to open it there; each scheduled run writes the week's page again in its own topic in their brainstorm folder.
4. **Say how it can run,** naming only what exists:
   - **A brief that runs on its own,** a workflow of kind `brief`, at its time, with its page in their brainstorm folder and its answer in their inbox ([workflows](vfs://guides/workflows.md)). [Monday Brief](vfs://guides/monday-brief.md) is the brief's template. After a yes, make their own with `kater new workflow "<Name>" --start brief --cron monday --timezone <zone> --file -`, such as "My Monday brief", whose step follows Monday Brief with the metrics and window you settled ("Follow vfs://guides/monday-brief.md, with these changes: …"). `--cron` also takes `weekday`, `friday`, `month` or a cron. It runs from then on, with the organisation's model key for scheduled workflows: there is no task to set up in their agent. [Schedule workflow](vfs://guides/schedule-workflow.md) changes, pauses or runs it.
   - **An app they open:** after a yes, the page becomes a draft app and moves to their apps ([brainstorm](vfs://guides/brainstorm.md), Draft apps). It reads fresh rows each time it opens.
   - **A scheduled Slack DM:** one saved query's figures, sent only to its owner after approval, with no model writing each digest. Follow vfs://guides/schedule.md, and confirm the owner's time zone and Slack connection. Never promise email, another person's delivery, or a model-written digest.
5. **Ask once** which to set up. After a yes, set up only that, and set the note's `Status:`.

Stop when none of their metrics has a verified definition: say which lack one, and offer [build query contract](vfs://guides/build-query-contract.md) to define the first.

## What a brief is for

A brief answers "what do I need to know, and what do I do about it?" in the time it takes to drink a coffee. Write it for one person, from `kater brief` and their about.md: the figures they pinned, how they like answers, their targets.

- **Insight first.** Lead with the one thing that matters most, as a sentence that says what happened and why, with its figures. Never lead with a table or a chart.
- **Few insights.** At most five under What moved, the biggest first. A move too small to act on goes in Held steady.
- **Against the usual, not last week.** Compare the latest complete week with the average of the four complete weeks before it, since a single week of email or SMS visits is small. Say so once, in the masthead.
- **Every figure is a tag.** Type no number into the page: each one is a `<kater-value>`, a frame the template draws or one of Kater's tags. A figure from outside Kater, which the person chose, is a `<kater-cite>` of its `<kater-source>` ([Sources](vfs://guides/app-sources.md#sources)). A count the prose needs, such as "six weeks in a row", is a claim the rows must support; check it before you write it.
- **Each insight ends somewhere.** A follow-up question answered from the same run, and, for the one thing, a next step with the starter to run, such as `/deep-analysis`.
- **True for this run.** The prose is written for the week it reports. A brief, a scheduled workflow of kind `brief`, writes the page again each run, so the words and the figures stay about the same week.
- **A run keeps what it cannot read.** A scheduled run runs every Kater figure again, but it cannot read another system. It keeps each linked figure, its `<kater-cite>` and its source's `read-at` as they are, and never writes a new date for a figure it did not read. Its final message names each figure it kept and the day it was read, so the person knows what to read again.

## Skeleton

```html
<body>
  <div class="digest-toolbar"></div>
  <main class="digest">
    <header class="digest-masthead">
      <p class="digest-eyebrow">Monday Brief</p>
      <h1 class="digest-title">Good morning, <em>Sally</em>.</h1>
      <p class="digest-dek">What moved in the week of <kater-value query="…/week_scorecard" field="session_start_date"></kater-value>, against the four weeks before it. <strong>The week in one sentence.</strong></p>
    </header>

    <section class="digest-lead" data-tone="down">
      <p class="digest-kicker">The one thing</p>
      <h2 class="digest-headline">Owned reach <em>slipped</em> to <kater-value query="…/week_scorecard" field="owned_or_organic_share_of_sessions"></kater-value> while paid filled the gap.</h2>
      <p class="digest-why">Why, in two or three sentences, with its figures.</p>
      <kater-frame query="…/weekly_trend" data-digest-spark="session_start_date:owned_or_organic_share_of_sessions" data-digest-target="owned_organic_share_target"></kater-frame>
      <kater-frame query="…/channel_moves" data-digest-bars="session_channel:channel_sessions_change_from_four_week_average" data-digest-mark="is_owned_or_organic_channel"></kater-frame>
      <div class="digest-followups">
        <details class="digest-followup">
          <summary>Is this more than one week?</summary>
          <p>The answer, from the same run, with its figures as tags.</p>
        </details>
      </div>
      <p class="digest-next"><span>Next step</span><span>Find out why: <span class="digest-command">/deep-analysis Why did organic search visits fall last week?</span></span></p>
    </section>

    <kater-frame query="…/week_scorecard"
      data-digest-figures="owned_or_organic_share_of_sessions:owned_or_organic_share_of_sessions_in_four_weeks_before,returning_identities:returning_identities_average_of_four_weeks_before"></kater-frame>

    <section class="digest-section">
      <h2 class="digest-section-title">What moved</h2>
      <ol class="digest-insights">
        <li class="digest-insight" data-tone="up">
          <p class="digest-kicker">Retention</p>
          <p class="digest-text"><strong><kater-value query="…/week_scorecard" field="returning_identities"></kater-value> visitors came back</strong> last week, against a usual <kater-value query="…/week_scorecard" field="returning_identities_average_of_four_weeks_before"></kater-value>.</p>
          <kater-frame query="…/weekly_trend" data-digest-spark="session_start_date:returning_identities"></kater-frame>
          <div class="digest-followups">…</div>
        </li>
      </ol>
    </section>

    <p class="digest-steady"><span>Held steady</span><span>The figures that barely moved, in one line.</span></p>

    <section class="digest-section">
      <h2 class="digest-section-title">This week</h2>
      <ol class="digest-actions">
        <li><label><input type="checkbox" /><span class="digest-action-text"><span>What to do</span><span>Why, with its figures, and the starter to run.</span></span></label></li>
      </ol>
    </section>

    <footer class="digest-footer">
      <p>Which workflow writes this page, and that every Kater figure runs again when it opens.</p>
      <kater-appendix folded></kater-appendix>
    </footer>
  </main>
</body>
```

## Blocks

| Block | What it holds |
|---|---|
| `.digest-masthead` | `.digest-eyebrow` (the brief's name), `.digest-title` (a greeting, the person's name in `<em>`), `.digest-dek` (the period, what it is compared with, and the week in one `<strong>` sentence) |
| `.digest-lead` | The one thing: `.digest-kicker`, `.digest-headline` (one word in `<em>`), `.digest-why`, its frames, `.digest-followups` and `.digest-next` |
| `<kater-frame data-digest-figures>` | The week in figures: three to five figures, each against its usual |
| `.digest-section` with `.digest-section-title` | What moved, This week |
| `.digest-insights > .digest-insight` | One insight: `.digest-kicker` (its subject, such as Email), `.digest-text`, at most one frame or tag, `.digest-followups`. The template numbers them |
| `.digest-steady` | What held steady, in one line, after a label |
| `.digest-actions` | This week's steps, each a checkbox the reader ticks while reading; nothing is saved |
| `.digest-footer` | What writes the page, and `<kater-appendix folded>`: the sources list folded under one Sources line, with the mock data count when there is mock data. Never put it in a `<details>` |
| `.digest-command` | A starter as it is typed, such as `/compare email value per visit` |

`data-tone` on the lead, an insight or a figure says which way it reads: `up`, `down` or `watch` (neither good nor bad yet). The kicker's dot and colour follow it.

## Figures, sparklines and bars

The template draws three frames from their queries' results, with labels and formats from the query's own fields, so the page types no figure and no label.

- **Figures.** `data-digest-figures="<field>:<usual field>,…"`. Each figure shows its value, its field's label, and its move against the usual field: in points for a percent, as a percent for anything else, and Steady for a move under 0.5 points for a percent, or under 2% for anything else. Add `:down` after the usual field for a figure whose fall is good, such as visitors who did not come back.
- **Sparkline.** `data-digest-spark="<period>:<field>"` over a query with one row per period, oldest first. `data-digest-target="<field>"` draws that field's value, from the first row, as a dashed marker. The latest point is marked with its value; the reader points at the chart, or uses the arrow keys, to read each period.
- **Bars.** `data-digest-bars="<label>:<value>"`, one bar per row, in the query's order. A set with a negative value diverges from zero in the middle and writes a sign; a set without one, such as rates, starts at the left. `data-digest-mark="<field>"` names a true or false field: rows where it is false are faded, so the rows the insight is about stand out. `data-digest-rows="<n>"` shows the first n rows.

A number inside a sentence is a `<kater-value>`. `row` says which row: leave it out for the total (the query's own `totals.total` pick of the field, which the warehouse works out; one row is its own total), name the row by its values in Compare's form, such as `row="platform_name=Google Ads"` (several joined with `|`), or give its place, counted from 0 in the order the query returns its rows. A dimension has no total, so its figure needs a row, unless the query returns one row: a query whose fields are all totals with no GROUP BY, or one that ends with `LIMIT 1` or `QUALIFY ROW_NUMBER() OVER (ORDER BY …) = 1`. Its contract says `at_most_one_row`, and every figure of it leaves `row` out. Check each row against this run's rows.

## When no frame fits

Show each figure with the first of these that fits it:

1. **A frame the template draws**, from the list above. A fix to the template reaches every brief.
2. **One of Kater's tags**: `<kater-chart>` with any chart kind, `<kater-table>` or `<kater-kpi>` ([app](vfs://guides/app-data.md)). The brief draws it with no card around it, 20rem tall; `data-height="short"`, `"medium"` or `"tall"` makes it 16, 20 or 28rem. When a KPI's figures or a Text chart's words need more room, it grows to show all of them.
3. **A custom chart**, `<kater-custom-chart>`, which the page draws with a script over one query's bound fields ([Custom charts](vfs://guides/app-data.md#custom-charts)), for a chart of one query that no chart kind can show. Kater draws its tooltip and its loading, and checks each figure on it.
4. **A frame the page draws itself**, with a script ([Custom visuals](vfs://guides/app-data.md#custom-visuals)), only for a visual that is not one query's chart. Kater still draws its loading, its errors and its cog.

Style the brief's own blocks, never what Kater draws. A page's `<style>` can size and place a tag, but the app check refuses a rule that selects inside a Kater tag, a class or attribute that Kater draws, such as `.digest-spark-plot` or `[data-slot]`, or a class that Kater's own styles use, such as `.grid`. Give the brief's blocks classes of their own.

## Writing the queries

Write the brief's queries in one workspace of the person's own, such as `monday_brief`, from the shared model their metrics come from. Each fixes its period from the rows, never from today's date, so the page and the run agree.

- **The week in figures.** One row: each figure for the latest complete Monday-to-Sunday week, grouped by `KATER__WEEK(m.date)`, and beside it a `CUSTOM_CALCULATION` of the four weeks before. A count's usual week is its average, `AVG(CAST(EVAL(m.count) AS DOUBLE)) OVER (ORDER BY KATER__WEEK(m.date) ROWS BETWEEN 4 PRECEDING AND 1 PRECEDING)`, with `@format number(0)`. A share or a rate adds up its parts over the four weeks, `SUM(EVAL(m.part)) OVER (…) / NULLIF(SUM(EVAL(m.whole)) OVER (…), 0)`, and is never the average of four weekly rates. Keep the latest week with `QUALIFY KATER__WEEK(m.date) = MAX(KATER__WEEK(m.date)) OVER ()`, and select each figure as `EVAL(m.figure)`: a query with `QUALIFY` takes a measure only through `EVAL`.
- **The weeks.** One row per week for the latest 12 complete weeks, oldest first, for the sparklines.
- **What moved it.** One row per category, such as channel: the latest week, the usual week, and the change, the biggest fall first.
- **Who to act on.** The audience a step names, such as visitors first seen in the last four weeks who have not come back.

Read the period with `KATER__IS_WITHIN_LAST(m.date, n, 'week', complete => TRUE, end => DATEADD(DAY, 1, (SELECT MAX(reported.date) FROM workspace.<model> AS reported WHERE reported.date < DATE_TRUNC('WEEK', CURRENT_DATE))))`, where `m` is the query's own alias of `workspace.<model>`. This is the window the field picker writes for the last n complete weeks of data, so a row dated in the current week doesn't move it. Each figure is a measure: a measure of the shared model, or a `MEASURE` in the workspace root, such as `MEASURE('email_sessions', COUNT(DISTINCT IFF(channel = 'Email', session_key, NULL)))`. Each query selects the measure by its own name, from the root, with no CTE between them, so the warehouse totals it again for each week and category. Never give a measure a second name with `DIMENSION` in a query: readers then see two fields for one figure, and the value can't be totalled at another grain. The page reads each figure by that name, such as `field="email_sessions"`.

## Motion and print

The page is whole at rest, as a thumbnail or a shared link shows it. The opening blocks rise in once as it loads, figures count up when they arrive on screen, bars grow as they are drawn, and blocks below the first screen settle in as they scroll in. There is no motion in a snapshot, in print, in a host's preview, or for a reader who asks for less. A brief prints and exports on pages as wide as its column: **File → Export** writes PDF, Word, Markdown and Excel.
