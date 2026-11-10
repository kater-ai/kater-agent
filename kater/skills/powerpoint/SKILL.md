---
name: powerpoint
description: "PowerPoint template. Use it when the person asks for: Slides, a deck."
---

This skill is Kater's guide `vfs://guides/powerpoint.md`.

## PowerPoint template

A deck is an app page laid out as widescreen slides. On screen each slide is 1280 by 720 pixels, shown smaller in a narrow window, with its speaker notes under it. **File → Export → PowerPoint** writes the deck out as a `.pptx` file that opens in PowerPoint, Keynote and Google Slides. Export also writes the deck as a PDF, a slide a page, as Markdown and as an Excel workbook of its figures. The page names the template on its root:

```html
<html lang="en" kater-template="powerpoint" data-theme="light">
```

The template is a stylesheet and a small script the runtime serves, never a copy in the app folder. It uses only the theme's tokens. `data-theme="light"` keeps slides white whatever the host's mode is.

## Skeleton

```html
<body>
  <div class="deck-toolbar"></div>
  <main class="deck">
    <section class="slide" data-layout="title">
      <header class="slide-header">
        <p class="slide-kicker">Weekly review</p>
        <h1 class="slide-title">Deck name</h1>
        <p class="slide-subtitle">Week of <kater-value query="…/scorecard" field="report_week_label"></kater-value></p>
      </header>
      <footer class="slide-footer"><span>Company, Team</span><span class="slide-number"></span></footer>
    </section>
    <aside class="slide-notes">
      <p>Talking points, with <kater-value …></kater-value> figures.</p>
      <kater-appendix for="slide" folded></kater-appendix>
    </aside>

    <section class="slide">
      <header class="slide-header">
        <p class="slide-kicker">Section</p>
        <h2 class="slide-title">A title that says what the slide shows, with <kater-value …></kater-value></h2>
      </header>
      <div class="slide-body" data-split="wide-narrow">
        <kater-chart query="…" kind="line" dimension="…" measures="…" heading="…" description="…"></kater-chart>
        <aside class="slide-callout"><h3>Where to act</h3><p>…</p></aside>
      </div>
      <footer class="slide-footer"><span>What the figures are</span><span class="slide-number"></span></footer>
    </section>
    <aside class="slide-notes">…<kater-appendix for="slide" folded></kater-appendix></aside>
  </main>
</body>
```

- A slide is a header, a body that takes the room left, and a footer. Plan each body for about 1150 by 430 pixels. A slide is judged as the file draws it, so check it in Static View (`static=1` in the page's launch URL): a slide whose content runs past its edge gets `data-overflowing`, and once its figures load the console names it; readers see no warning, so move a block to another slide. A `<kater-table>` fills its cell and shows the rows that fit, then a line saying how many of how many it shows; return fewer rows (top N) when every row matters.
- Each slide's notes are the `<aside class="slide-notes">` right after it. End every slide's notes with `<kater-appendix for="slide" folded>`: the slide's sources, folded on screen under one Sources line with its figure count, and the mock data count when the slide has mock data. The runtime adds one to notes that lack it, so a slide without notes still gets its sources, but write it so the page says so. The notes go into the file's speaker notes, never onto the slide: the talking points, then one line a figure, grouped by source.
- **Show citations**, a setting under **Export**, shows or hides each figure's number on the slides, once per slide. A deck hides them by default, since it is presented; an export writes whichever it shows.
- `.slide-number` gets the slide's number as text, so the file's slides show it too.
- In Kater the slides are live: a chart answers hover with its tooltips, its settings open it in place, and a table scrolls. The file is a snapshot, so every export draws the slides static while it writes, as in the [pdf template](vfs://guides/pdf.md): nothing opens and nothing is cut short. **Static**, in the page's own corner cluster when it is opened on its own, shows the slides as the file will have them. The page never marks itself static.
- `.deck-toolbar` holds only what the runtime puts there: the **Live** / **Static**, **Copy as Markdown** and **Export** commands, with **Show citations** as a setting under Export. In the portal the page is always live, Copy as Markdown and Export sit in its Edit and File menus, and the toolbar stays empty; opened on its own, they float in a small cluster in the window's top corner. The file is written in the theme on screen, which the portal's View → Theme Mode sets. A deck has no filters or parameters. The file keeps the figures but not the control that chose them, so neither the slides nor their notes could say which value made them.

## Blocks

| Class or attribute | What it draws |
| --- | --- |
| `data-layout="title"` or `"section"` on a slide | The theme's primary colour edge to edge, the header at the foot |
| `slide-header`, `slide-kicker`, `slide-title`, `slide-subtitle` | The top of a slide |
| `slide-body` with `data-columns="2|3|4"` or `data-split="wide-narrow|narrow-wide"` | Blocks side by side, each filling its cell |
| `slide-stack` | Blocks in a column inside one cell |
| `slide-callout` with `data-tone="positive|negative|warning|neutral"` | A finding or an action, set off by a rule |
| `slide-bullets` (`ul` or `ol`) | Short statements, one per line |
| `slide-figure`, `slide-figure-label`, `slide-figure-value`, `slide-figure-detail` | A headline figure the page writes from `<kater-value>`s |
| `slide-lede`, `slide-note` | A larger opening sentence, and small print |
| `slide-footer` | The foot of a slide: what the figures are, and the number |
| `data-height="short|medium|tall"` on a tag | A height of 180, 240 or 360 pixels instead of filling its cell. When a KPI's figures or a Text chart's words need more room, the tag grows to show all of them, and a slide that then has more than fits is marked `data-overflowing` |

## What the file holds

- Every word is a text box and every box a shape, where the page shows it, so a presenter edits anything in PowerPoint or Google Slides. Fonts are set to Arial, which both have.
- A line, bar, area, composed or pie chart is a native chart drawn from the figures its tag drew, in the page's colours. In PowerPoint, Edit Data opens its figures in Excel. Google Slides imports a native chart as a picture of it; its text, tables and figures stay editable.
- A table is a native table.
- A figure's number, when the Sources switch shows it, stays a link to its query in Kater's editor, and each slide's notes end each query's sources with an Open in Kater and an Open in VS Code address.
- Each slide's notes hold its talking points, then every figure on it, one line each under the source it came from: mock data first, with why, then From Kater, with each query's model, workspace and file once per query and "(Verified)" after a figure from the shared layer, then From other sources, with each system, the place in it, its URL and its read date.
- A mock data figure shows MOCK DATA on its slide, and a deck with mock data asks before any export saves it.

## Writing the words

A deck is a document: a workflow writes its words with each run. Put each sentence that judges the numbers in an empty `<kater-words name="…">` on the slides, never as typed text, and write the workflow with the app ([Make a document](vfs://guides/projects-documents.md#make-a-document)). A heading, a label and a sentence that only says what a figure is stay on the page, with each figure a `<kater-value>`.

## Writing the queries

- Every figure, period and scenario on the slides comes from a query. Write the report week as a column and show it with `<kater-value>`; never type a date or a number into the page.
- A weekly deck reads the latest complete week in the data, found from the rows, so it is current each Monday with no input. Never take the period from a parameter: a deck sets none, so a date parameter runs at its default and the deck stays on that week.
- A scenario, such as a spend change, runs at its parameter's default. Say which one on the slide with `<kater-value>` from a column. To compare scenarios, return one row per scenario.
- A chart's figures must read as measures: a measure, an evaluated value (`EVAL(…)`, or arithmetic of evaluated values inside a CTE), or a custom calculation. Arithmetic on a CTE's columns in the final SELECT reads as a dimension, and a chart refuses it.
- A line or bar chart takes at most two measures, and a composed chart four. For one line per platform, return a row per week and platform and name one measure: the chart draws a line for each platform.
- Text a query does not return, such as a recommendation, names the row it reads: row 0 of a query ordered by the figure it ranks.
