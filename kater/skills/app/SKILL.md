---
name: app
description: "HTML apps. Use it when the person asks for: A custom page or report that combines workspaces, to share; \"Document this conversation\", \"send the team what we found\", a record of questions and answers; A printable report, a PDF; Slides, a deck; A brief to read first thing, such as each Monday."
---

This skill is Kater's guide `vfs://guides/app.md`.

## HTML apps

An app is the HTML, CSS and JavaScript in one folder, `apps/<app-name>/` beside your `workspaces/` folder, served by the runtime with no build. `kater.app.yaml` is its manifest, `index.html` is its page, and the page may load other files from the same folder by relative path.

The runtime puts three things ahead of the page's own code: the app's theme as CSS variables, an import map of the libraries it serves, and the `kater` browser API with Kater's tags. Style with the variables, such as `var(--background)`, `var(--foreground)`, `var(--primary)`, `var(--chart-1)` to `var(--chart-5)`, `var(--font-sans)` and `var(--radius)`, never with literal colours or fonts. Style the page's own markup and the box of each Kater tag, such as its height or its place in a grid, never what Kater draws: the app check refuses a `<style>` rule that selects inside a Kater tag, or a class or attribute that Kater or a template draws, such as `[data-slot]` or `.digest-spark-plot`, since a fix to how Kater draws it would then miss the page. It also refuses a rule that names a class Kater's own styles use, such as `.grid`, `.flex`, `.hidden` or `.truncate`: Kater's charts, tables, tooltips and menus draw with them, also in layers on the page's body, so the rule would change them too. Give the page's markup classes of its own, such as `budget-grid`. The page cannot load anything from another host or send anything to one, open a window, or submit a form.

Draft it in your brainstorm folder first and show it there ([brainstorm](vfs://guides/brainstorm.md)): one page is one `.html` file, with no manifest, and an app the user would keep, such as a deck, is a draft app in the topic's `apps/<app-name>/`, which moves to your apps with `kater app apply` only after the user says yes ([Draft apps](vfs://guides/brainstorm.md#draft-apps)).

A file or a mention names an app by its folder from the content root, such as `connections/<connection>/users/<email>/apps/<app>`. A `kater` command and a portal address take its key, `<connection>/users/<user_id>/<app>`, or its id (`rel1_…`), and a command takes its folder too. An app over one workspace, the person's or the connection's, is in that workspace's `apps/<app>/`, and its key has `workspaces/<workspace>/` before its name, such as `<connection>/workspaces/<workspace>/<app>`. A draft app has no id. `kater app list` and `kater app search` give each app's folder, key and id. A page is named by its file's name, such as `index`.

To show an app that exists, run `kater brainstorm show` on its folder, such as `connections/<connection>/users/<email>/apps/<app>`, or on one of its pages. It says whether each tag draws, on an installed app's page what the page showed as it was last drawn after a change (`drawn`), and in a chat that draws apps it shows the app itself.

## The steps

Read each part when you reach its step.

1. **Show the data** with Kater's tags, filters, comparisons, custom charts and custom visuals: [showing data](vfs://guides/app-data.md).
2. **Cite each figure's source,** and ask the person when Kater does not have the data: [sources](vfs://guides/app-sources.md).
3. **Make the pages, the folder, the manifest and the theme, and share the app:** [pages, sharing and the folder](vfs://guides/app-pages.md).
