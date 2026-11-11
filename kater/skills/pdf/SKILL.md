---
name: pdf
description: "PDF template. Use it when the person asks for: A printable report, a PDF."
---

This skill is Kater's guide `vfs://guides/pdf.md`.

## PDF template

A PDF report is an app page laid out as printable sheets. On screen each sheet is a piece of paper on the page's background; printed or saved as a PDF, each sheet is one page. The page names the template on its root, and the runtime links the template's stylesheet before the page paints:

```html
<html lang="en" kater-template="pdf" data-theme="light">
```

The template is a stylesheet the runtime serves, never a copy in the app folder, so a fix reaches every report. It uses only the theme's tokens. `data-theme="light"` keeps paper white whatever the host's mode is.

## Skeleton

```html
<body>
  <div class="report-toolbar"></div>
  <main class="report" data-paper="letter">
    <section class="report-page">
      <div class="report-running-head"><span>Company, Team</span><span>Report name</span></div>
      <header class="report-masthead">
        <p class="report-kicker">Team, Audience</p>
        <h1 class="report-title">Report name</h1>
        <p class="report-subtitle"><kater-value query="…/scorecard" field="report_period"></kater-value></p>
      </header>
      <p class="report-lede">Authored wording with <kater-value query="…" field="…"></kater-value> figures.</p>
      <section class="report-section">
        <kater-chart query="…" kind="composed" dimension="…" series="…" heading="…" data-height="medium"></kater-chart>
      </section>
      <footer class="report-footer"><span>Report name</span><span class="report-page-number"></span></footer>
    </section>
    <!-- one <section class="report-page"> per sheet, then the appendix -->
    <section class="report-page report-appendix">
      <kater-appendix></kater-appendix>
    </section>
  </main>
</body>
```

- `data-paper` is `letter` (default) or `a4`; `data-orientation="landscape"` turns the sheets.
- `.report-toolbar` holds only what the runtime puts there: the page's **Live** / **Static**, **Copy as Markdown** and **Export** commands, with **Show citations** as a setting under Export. In the portal the page is always live, Copy as Markdown and Export sit in its Edit and File menus, and the toolbar stays empty; opened on its own, they float in a small cluster in the window's top corner. A report with mock data asks before any export saves it. **Export → PDF** opens the browser's print dialog, where each sheet is one page; **Export → Word** writes a `.docx` in which each sheet starts a page, each chart is a native Word chart and each table a native table, and **Export → Markdown** and **Excel** write the report's words and figures. It is never printed. The report prints in the theme on screen, which the portal's View → Theme Mode sets. A report has no filters or parameters. Paper keeps the figures but not the control that chose them, so neither the sheets nor the appendix could say which value made them. The period comes from the rows (see "Writing the queries").
- A sheet's content area is about 700 by 940 pixels on Letter. Plan each sheet to fit. A sheet is judged as it prints, so check it in Static View (`static=1` in the page's launch URL): a sheet that outgrows its paper gets `data-overflowing`, and once its figures load the console names it; readers see no warning, so move a block to the next sheet.
- The footer is pinned to the foot of the sheet, and `.report-page-number` counts sheets.
- End every report with an appendix sheet, `<section class="report-page report-appendix">` holding `<kater-appendix>` and nothing else: the list writes its own Sources heading, and the template writes the line that says what its numbers are. It lists every number the sheets carry, mock data first, then From Kater and From other sources, in one list as wide as the sheet, and runs onto as many printed pages as it needs. Never give it a heading of your own, such as "Where the figures come from", or write sources by hand anywhere on the sheets.

## Static sheets

In Kater a report is live: a chart answers hover with its tooltips, its settings open it in place, and a figure in a sentence opens where it came from. What leaves Kater is paper, so every export draws the sheets as a snapshot, and **Static**, in the page's own corner cluster when it is opened on its own, shows exactly that on screen. The page never marks itself static. Drawn static, every Kater tag answers no pointer and hides nothing:

- A card has no toolbar, settings button or hover lift, and a frame has no settings button.
- A chart or KPI card is drawn flat on the paper: a rule above it instead of a box around it, its heading in line with the sheet's text.
- A figure in a sentence, and a figure named in a title, has no dotted underline or mark promising a card that never opens. It carries a small number instead, once per sheet, and the appendix lists it under its source with what its hover would have: its formula or definition under its line, and the query, file or system it came from. A name, such as a channel, carries none.
- A figure from the shared layer keeps its green badge, and a mock data figure keeps its MOCK DATA badge, which Hide Citations never hides. A link prints as its text.
- A chart, table or KPI lists every figure it draws in the appendix.
- **Show citations**, a setting under **Export**, shows or hides the numbers, and what it chose holds for every export. A report shows them by default.
- Charts have no tooltips or legend toggles.
- Nothing is cut short. Text wraps, a category axis makes room for its widest label, crowded bar charts lie on their side so each label has a row, and axis titles give way to the card's heading.

When a tag drawn static still cannot show something whole, such as a chart whose card is too short for its legend, the runtime marks it `data-kater-clipped` and names the cut text in the console; readers see no warning mark: fix the source (see "Labels on paper") or the figure's size.

## Blocks

| Class or attribute | What it draws |
| --- | --- |
| `report-running-head`, `report-footer` | The small line at the top and foot of a sheet |
| `report-masthead`, `report-kicker`, `report-title`, `report-subtitle` | The block that opens the report |
| `h2`, `h3`, `p`, `report-lede`, `report-note` | Document type; `report-lede` is the opening sentence |
| `report-section` | A heading and the figures under it |
| `report-grid` with `data-columns="2|3|4"` or `data-split="wide-narrow|narrow-wide"` | Figures side by side; one column on a phone |
| `report-figure` with an `h3` and `figcaption` | A frame or table with its heading and a source line |
| `report-callout` with `data-tone="warning|positive|negative|neutral"` | A finding or an action, set off from the flow |
| `report-steps` (`ol`) | Numbered next steps |
| `report-facts` (`dl`) | Labels and their values |
| `report-badge` with `data-tone` | A short status label |
| `report-keep` | Anything that must not split across a page break (tags and sections already don't) |
| `report-signoff` | Prepared by and reviewed by lines |
| `data-watermark="Draft"` on a sheet | A diagonal word across it |
| `data-height="short|medium|tall"` on a chart or KPI tag | A card height of 256, 320 or 384 pixels, so a sheet's budget is known. `tall` holds nine horizontal bars or a pie. When a KPI's figures or a Text chart's words need more room, the card grows to show all of them |

## Figures and tables

Headline figures and tables on paper are drawn by the template inside a `<kater-frame>`, so they print as text rather than as a screen widget:

```html
<kater-frame query="…/scorecard" heading="Headline figures"
  data-report-figures="lifecycle_sessions:lifecycle_sessions_prior,owned_organic_share:owned_organic_share_prior"></kater-frame>

<figure class="report-figure">
  <h3>Email and SMS campaigns</h3>
  <kater-frame query="…/lifecycle_campaigns" heading="Lifecycle campaigns"
    data-report-table="session_start_date,session_campaign,campaign_sessions"></kater-frame>
</figure>
```

- `data-report-figures` lists fields, each optionally followed by a colon and the field it is read against. Labels and formats come from the query, so label a prior-month field `Prior month`: it prints as "Prior month: 147". The figures share their rows, so a label that wraps keeps every figure level.
- `data-report-table` lists the columns in order; an empty list shows every field. Numbers are right-aligned.
- `<kater-kpi>` and `<kater-table>` print too. A KPI card shows its figure and, under it, the field in the same place in `prior`: `metrics="a,b" prior="a_prior,b_prior"`. Leave a place empty for a card with no prior: `prior=",b_prior"`. On a static sheet a table is as tall as `--report-rows` rows (8 unless the page sets it, as `style="--report-rows: 12"`) and shows the rows that fit, then a line saying how many of how many it shows. A `data-report-table` frame writes out every row.

## Charts on paper

- A chart takes the full width of whatever holds it.
- On a static sheet a chart fits its card and never scrolls: an axis of dates shows every period and labels as many as fit, and a legend shows every entry. A card too small for its plot, legend and labels is marked `data-kater-clipped`; give it a taller `data-height` or a wider column.
- To show a figure against a goal, return the goal as a column and draw `kind="composed"`: the figure as bars and the goal as a line.
- A pie or a bar chart of a few categories fits any width.

## Labels on paper

A label on paper is read once and cannot be hovered, so ask the query for labels written for print:

- Return a display name, not a raw code: `TikTok ads` rather than `tiktok / paid_social`, with a `CASE` in the query and the raw value as its `ELSE`.
- Keep category labels to about 16 characters and a chart to four series or fewer.
- Give each column the short label a reader needs, such as `Share of sessions` and `Target`, since a chart's legend and a table's header print it.

## Writing the words

A report is a document: a workflow writes its words with each run. Put each sentence that judges the numbers in an empty `<kater-words name="…">` on the sheets, never as typed text, and write the workflow with the app ([Make a document](vfs://guides/projects-documents.md#make-a-document)). A heading, a label and a sentence that only says what a figure is stay on the page, with each figure a `<kater-value>`.

## Writing the queries

- Every figure, period and goal on the sheets comes from a query. Return the period the report covers as a column and show it with `<kater-value>`; never type a month or a number into the page.
- Find the report's month from the rows: the latest complete month in the data. Never take it from a parameter. A report sets none, so a date parameter runs at its default and the report stays on that month.
- Give each query its own window as a `REQUIRED_FILTER` over whole months, ending with the latest complete month. `end` is the day after the latest day the data reports before the current month, as the field picker writes a window that ends at the latest reported date. A month whose last day is in the data counts as complete, and a row dated in a later month doesn't move the window:

```sql
REQUIRED_FILTER(
  'report_and_prior_month',
  KATER__IS_WITHIN_LAST(
    m.session_start_date,
    2,
    'month',
    complete => TRUE,
    end => DATEADD(
      DAY,
      1,
      (
        SELECT
          MAX(reported.session_start_date)
        FROM workspace.marketing_sessions AS reported
        WHERE
          reported.session_start_date < DATE_TRUNC('MONTH', CURRENT_DATE)
      )
    )
  ),
  label='Latest complete month and the month before'
)
```

- A window that ends earlier, such as cohorts given 60 days to return, moves that `end` back by whole months: `DATEADD(MONTH, -2, DATEADD(DAY, 1, (SELECT MAX(…) … WHERE … < DATE_TRUNC('MONTH', CURRENT_DATE))))`.
- A goal, such as a target share, is a parameter's default or a column, never a control on the page.
- Put the report's queries in one workspace, and read other workspaces' queries beside them, such as a paid-media query.
