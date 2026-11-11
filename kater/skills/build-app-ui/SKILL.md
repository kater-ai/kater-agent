---
name: build-app-ui
description: "Build or change an app a stakeholder opens. Use it when the person asks for: an app, a page for someone, a report as a document."
---

This skill is Kater's guide `vfs://guides/build-app-ui.md`.

# Build an app

Build or change an app: the stakeholder interface in `apps/<app-name>/`, which reads queries from one or more workspaces. Composition knowledge and the surface rules live in the guide; this file defines the build contract.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

Use this when a workspace needs a stakeholder-facing surface built or changed — a page, a dashboard, a form, or the application shell around them. Do not use it to author a query contract, a semantic model, or any other governed object; a surface only renders what those already return.

## Procedure

1. **Read the guide first:** vfs://guides/app.md. Never build a surface from memory.
2. **Read the surface rules:** "Folder, manifest and theme" and "What a surface shows" in vfs://guides/app-pages.md. They govern what may appear on a stakeholder surface, and they are not restated here.
3. **Confirm the app and what it reads.** Know which app it is and the workspaces it reads. A personal app lives in the person's own folder, `connection.folder` of `kater_start`, at `connections/<connection>/users/<email>/apps/<app-name>/`, beside `workspaces/`, never inside a workspace, and its tags name each query as `<workspace>/<query>` and each saved chart as `<workspace>/<chart>` (`data-kater-chart`): the workspace's folder from the content root and the declared name, never the file, also on a `<kater-compare>` side. An app over one workspace lives in that workspace's `apps/<app-name>/`, the person's own or the connection's. A file or a mention names an app by its folder from the content root; a `kater` command takes its key or its id, which `kater app list` gives. Draft a new app in a brainstorm topic first, `brainstorm/<topic>/apps/<app-name>/`, with a `kater.app.yaml` naming it and its pages, and show it with `kater brainstorm show`; after the user's yes, `kater app apply` moves it to their apps (vfs://guides/brainstorm.md, Draft apps). A query a page needs that no workspace has is tried in a copy in the same topic, and the copy is applied before the app moves. Do not create package/build files or add it to the root pnpm workspace.
4. **Inventory before inventing.** An app page is HTML that the runtime draws with app-kit's components, through Kater's tags: every chart, table, figure, filter, source and row of tabs is a tag (the tag table in vfs://guides/app-data.md). A page cannot import app-kit, so use the tag before you write any markup of your own. Never draw tabs, a navigation bar, a filter, a card or a table yourself; the boundary checker rejects a local copy of app-kit's components.
5. **Find each figure's data, in this order.** First a Kater query that already has it: search the catalog, the workspaces first, then the shared models. Then a query you write on data in Kater, in the app's workspace; over a shared model, its figures still get the green badge. Only when no model in Kater has the data, ask: run `kater app ask <app> --request-file -` and show its question with your own question UI, or read the person's `answer` where the chat asked them already. Never use another source without the person's answer. Write the answer as the figure's source: the selected option's `writes`, each part in braces filled from the answer (vfs://guides/app-sources.md, on data that is not in Kater).
6. **Compose, then bind.** Compose the page from Kater's tags and bind every part of it, narrative lines included, to a query, so the page is data a reviewer can read. A workspace's declared filters and parameters are a `<kater-filter>`. A workspace's saved chart is a `<kater-chart>` over its query when it shows the intended view. A chart of one query that no kind draws is a `<kater-custom-chart>`, whose `fields` binds each field the page's script draws as `channel=field` ("Custom charts" in vfs://guides/app-data.md); its script never holds figures of its own. An app with several pages or sections moves between them with `<kater-tabs>` ("Pages and appearance" in vfs://guides/app-pages.md). Author a missing business computation in the query, never in the page.
7. **A deck or a report is a document.** A page built from the `powerpoint` or `pdf` template gets its workflow in the same change, even when the person wants it once. Each sentence that judges the numbers is an empty `<kater-words name>`, and the workflow at `connections/<connection>/users/<email>/ai_tools/workflows/<app-name>.md` (the app's name, with hyphens) has `writes` with the app's id, the steps that write a snapshot, and no schedule ("Make a document" in vfs://guides/projects-documents.md). Run it once, so the person has a first snapshot to read and add to the timeline. Any other app is a tool, with no workflow and no judgements.
8. **Run the checks.** `kater brainstorm show` on the app's folder must say every tag draws, with no warning left, such as a figure typed outside a tag, a script that holds a list of numbers or two custom charts that a problem can't tell apart. On an installed app with a custom chart, run it on each such page and do what its `drawn` `says`: while it is `drawing`, show the page again in a minute; at `checked` it lists what the drawing found there, such as a figure that no query holds, and none may be left; any other state is drawn later, so say so and don't wait for it. For a draft, `kater app apply FOLDER --dry-run` runs every check the move makes, app-kit's boundaries included, and names each problem with its page and line.

## Rules

- Every visual part of a page is a Kater tag, which app-kit draws. The page writes only its layout and words. Never copy app-kit's components, styles, `ui/`, `lib/utils`, the shell, result views or chat framing into an application; a copy fails the boundary check.
- Never author credentials, generated output, lockfiles, package-manager internals, or launch/session integration.
- A value the run did not return renders as an explicit gap. Never type a number, a date, or a date range into copy.
- A tool's sentences hold tags and no judgements. A sentence that judges the numbers goes in a document's `<kater-words>`, or on the tool's timeline as a dated note. A number typed into a page you make, such as `$1,204`, fails the app check. Each write of the page names it in `page_problems`: fix it in your next edit.
- Cite every figure. A figure from a query is its Kater tag; any other is a `<kater-cite>` of a `<kater-source>` on the same page that says every part its origin needs ("Sources" in the guide). Never write sources by hand: no "Sources:" line, and no `<details>` around `<kater-appendix>`.
- Ask before you use any source outside Kater, and use only the source the person chose.
- Cite a URL only when a tool gave it or the person did, and say which in `via`. Never make one up or complete one.
- Never write a new `read-at` for a figure nobody read again. Another system is never read on its own, so a linked figure keeps its date until someone reads it again.
- Never mark a figure verified. The green badge follows the field: only a field from the shared layer that a person reviewed gets it.
- Clipped text hands the whole value back through app-kit's `TruncatedText`, never a native `title` attribute and never a hand-written truncate class.
- The browser receives no warehouse, MCP, model-provider, or token-broker credential, and no raw SQL, relation name, or predicate.
- A reusable presentation need is an app-kit addition, a Kater tag with data-only attributes, not markup of the page's own. Design it as if the package were already published.

## Output contract

Report, for the calling artifact:

- `## Surface` — the exact `${workspace:...}` ref, the application path, and the pages or sections added or changed.
- `## Bindings` — one row per part of the surface and the contract or source feeding it.
- `## Checks` — the commands run and their actual result.

| Part | Kind | Contract | Status |
| --- | --- | --- | --- |
| `revenue-by-region` | chart section | `${query:example}` | bound / gap |
| `usage-growth` | figure in a sentence | `<kater-source id="usage" origin="mock">` | mock data |

A part with no contract and no source the person chose behind it is a gap, not a finished surface. Report it as one rather than filling it with typed copy. Report each mock data figure too, with the question for the data team, since it waits for real data.
