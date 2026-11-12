---
name: portal
description: "The Kater portal: every page, and how to take the person there. Use it when the person asks for: \"Where do I …\", \"take me to …\", \"show me … in Kater\", a page of Kater to open."
---

This skill is Kater's guide `vfs://guides/portal.md`.

# The Kater portal: every page, and how to take the person there

Read this when the person asks "where do I …", "take me to …", "show me … in Kater" or "open …". It names every page of the portal, what a person does there, and the keys of its address. Each address keeps everything on screen, so one address opens the page exactly as the person needs it.

## Open a page in one call

- **Link every page you name.** Write its name as a Markdown link to the `app_url` that a result gives, such as `You can open [the conversation](app_url) in Kater.` `kater place ADDRESS` answers a page's `app_url` and moves nothing, and `kater place --path PATH` does the same for an object. Each `kater new` answers where the new object opens: `url`, its address from the portal's root, and `link`, the whole link, which the `kater` tool also gives as `app_url`. Link that; it needs no `kater place`. Never write an address for the person to copy. A link moves nothing, so give it without asking.
- **Show a page with `kater open --new`.** Give it the page's address from the portal's root, such as `kater open --new "/admin/conversations?conversation=<id>"`, or an object's path, such as `kater open --new connections/<connection>/workspaces/<workspace>`, which opens the object on its own page. A new tab leaves the person's tabs where they are, so the `kater` tool opens it with no question: show a finished result this way, then say in one line what you opened. `--set KEY=VALUE` changes one key of the address, such as `--set share=1`. One conversation opens at most 5 new tabs in 10 minutes.
- **Move a tab only when the person asks.** `kater open` without `--new` moves this conversation's own tab, the one the person asked you from or one you opened, else opens a new tab: it never moves a tab you were not asked from and did not open. The `kater` tool refuses it: run the same command with kater_apply once the person says yes. In a shell, as when the Kater MCP is not connected, `kater open` keeps the same rule: it opens as that agent, and `kater here` marks that agent's own tab. Ask the person before you move a tab there too.
- **Use the address as this guide writes it.** A path segment in angle brackets, such as `<app key>`, is a value you put in its place. Every other part is fixed. Kater refuses an address that is no page, and its answer names the page nearest to it. Do not guess an address: find the page here.
- **Read where the person is with `kater here`.** It names each tab's page, what it has open and how it is set up. Read it before you move a tab, and after, when the person asks what they see.
- **Take the person back where they came from.** Each tab in `kater here` has its `trail`: the pages it went through to reach the page it shows, oldest first, each with its name, what was open in it and its `url`. "Go back", "the workspace I was in" or "the one before" means the last step: `kater open` with its `url` opens it as they left it. A tab opened from Home, a list or a shared link has no trail.
- **Some state is not in an address.** What a person types, a draft, a scroll position, a credential and where they came from never are.
- **A scheduled run gives links and never opens tabs.** Nobody is in a scheduled run's conversation to look at a page, so `kater open` refuses it, with or without `--new`. Put each page's link in the run's answer, from `kater place`.
- **No tab opened?** `kater open` says why, for example when the person paused sharing, has no Kater tab open, or the conversation opened too many tabs. Only then give the person the link from its `app_url`.

## The questions people ask most

| The person says | Open |
| --- | --- |
| "Show me this conversation in Kater" | `/admin/conversations?conversation=<id>`, where the id is the `audit_thread_id` that `kater_start` gave you. Only a person who can read the audit log sees the page. |
| "Where do I see every conversation people had with Kater this week?" | `/admin/conversations?window=7` |
| "Are people getting correct answers from Kater? What should I fix?" | `/admin/conversations`, which opens on What works and What to fix for the last 30 days. |
| "Which conversations used fields the data team does not share?" | `/admin/conversations?verdict=outside` |
| "Who can open this workspace? Take me to where I change that." | The workspace's page with Share open: `kater open connections/<connection>/workspaces/<workspace> --set share=1`. An admin can also see every folder's access at `/admin/settings/access?path=<path>`. |
| "What waits for me?" | `/inbox`, with `filter=needs` for only what needs the person |
| "Which alerts do I have?" | `/alerts`, with `alert=<id>` for the form of one alert |
| "Where did the query I deleted go? Can I get it back?" | `/trash`, with `item=<trash id>` for one deleted object, and its Restore |
| "What reads something that was deleted?" | `/trash?tab=fix`, or for an admin every delete at `/admin/objects?status=fix` |
| "Where are my requests to the data team?" | `/requests` |
| "Show me the result" | `/results/<result id>` |
| "Where do I connect Slack?" | `/admin/settings/integrations/slack` for the organisation, `/profile?slack=account` for the person's own account |
| "Who uses Kater, and what do they ask?" | `/admin`, the admin console's dashboard |
| "Which AI apps do people use with Kater?" | `/admin`, Connected apps on the admin console's dashboard. Set the apps up at `/admin/settings/ai?tab=mcp`. |
| "What has no owner?" | `/admin/objects?status=orphaned` |
| "Where do I change a semantic model? Who can?" | `/shared-model/<connection>/<kind>/<name>` (`<kind>` is `semantic` or `kater_dataset`), or `/shared-model` to find one; the portal's sidebar names it Shared models. A person with `models:write` (the Admin and Developer roles) and edit access to the connection's `modeling/` folder changes it. Anyone else sees it View only, and an Admin gives edit access. |

## Every page

### Work with data

| Page | Address | What a person does there | Keys |
| --- | --- | --- | --- |
| Home | `/` | Searches everything in the connection, fields included, opens what they pinned and opened last, and shows For you, the three things Kater's code picked for them that day. Its `q` can hold `key:value` filters, such as `type:field owner:me`, which the field draws as pills. | `q` |
| Daily brief | `/brief` | Reads their daily brief, a page under Home that Kater's code writes each morning from the figures they watch, their projects and what waits for them, or sets it up. | `brief`, `sources` |
| Get started | `/get-started` | A new admin copies the prompt for their agent, which sets up their organisation, and follows the setup. Every member sees what is for everyone at the organisation and what is their own. | `install`, `step` |
| Workspaces | `/workspaces` | Finds a workspace: their own, or one shared with them. | `show`, `q`, `layout`, `new`, `from` |
| Workspace | `/workspace/<workspace path>` | Works on a workspace's queries: rows, charts, SQL, filters and History. The workspace's name opens its menu, whose first row opens the workspace's details under the name. The query's name over the Editor opens the query's menu the same way: its details, New › Scratch from this query, Duplicate, Add as test and Open › This query in Files. Each filter's switch and condition on the page are the person's own, for this page only. | `file`, `model`, `chart`, `panel`, `show`, `sql` (`open`), `ws` (`sql` for the workspace's SQL tab on `workspace.sql`), `sidebar` (`definitions` for the workspace's Definitions on any page), `share`, `view`, `param.*`, `filters`, `off`, `own`, `op.*`, `val.*`, `column` and `row` (the active cell of the rows, which can be a blank row under them), `bar` and `filter_bar` (`open` keeps the formula bar or the filter row expanded), and more in the address list |
| Dashboard | `/workspace/<workspace path>?view=dashboard` | Reads a workspace's dashboard, edits it with `mode=edit`, and compares snapshots and periods. A snapshot is view-only, so `mode=edit` does nothing there. | `mode`, `snapshot`, `snapshot_note`, `periods`, `grain`, `compare_query` (Compare for one query; `query` is the Editor's), `between`, `takeaways`, `takeaway` |
| Shared models | `/shared-model` | Finds the shared semantic models and Kater datasets of a connection. | `q`, `group`, `new` |
| A shared model | `/shared-model/<connection>/<kind>/<name>` (`<kind>` is `semantic` or `kater_dataset`) | Reads or edits one semantic model or Kater dataset: its joins, fields, rows, tests and History. A reader who cannot change it sees it View only. | `view`, `sidebar`, `panel`, `grid`, `test`, `share` |
| Explore | `/explore` | Explores a shared model's cards, a start for a new workspace. | `source`, `view`, `mode` |
| Files | `/files/<path>` | Opens any file or folder the person can read, as in an editor. | `tabs`, `sidebar`, `panel`, `history`, `change`, `connection` |
| A link from outside Kater | `/to/<kind>/<ref>?view=<view>&at=<at>` | The one link to an object from outside Kater. It moves on to the object's page now, after a move or a rename. `/to/file/<path>?at=L<n>` opens a file where Kater shows it. | `view`, `at` |
| Brainstorm page | `/sandbox/brainstorm/<connection>/<email>/<path>` | Reads one page or note of a person's brainstorm folder. | `tab`, `param.*`, `filters`, `off`, `op.*`, `val.*` |
| Result | `/results/<result id>` | Reads a saved query result, with its chart. | `kind` |

### Apps and projects

| Page | Address | What a person does there | Keys |
| --- | --- | --- | --- |
| Apps | `/apps` | Finds an app. `/apps/mine` shows their own apps, `/apps/shared-with-me` the apps shared with them. | `q`, `new` |
| App | `/app/<connection>/users/<user id>/<name>/<mode>/<panel>`, `/app/<connection>/workspaces/<workspace>/<name>/<mode>/<panel>`, `/app/<connection>/users/<user id>/workspaces/<workspace>/<name>/<mode>/<panel>` | Reads an app (`viewing`) or edits it (`editing`), on one of its pages, with its filters. The panel is the toolkit's tab, such as `sources`, `history`, `snapshots` or `projects`. A snapshot is view-only, so the app opens in `viewing` there. An app is named by its key, `<connection>/users/<user id>/<name>`, or over a workspace `<connection>/workspaces/<workspace>/<name>` or `<connection>/users/<user id>/workspaces/<workspace>/<name>`, as `kater app list` gives it. | `tab`, `param.*`, `filters`, `off`, `op.*`, `val.*`, `select`, `slide`, `snapshot`, `snapshot_note`, `periods`, `share` |
| Project | `/projects/<connection>/users/<user id>/<name>` | Follows a goal: where it stands, its metrics, its timeline and its apps. A project is named by its key, `<connection>/users/<user id>/<name>`, as `kater project list` gives it. | `entries`, `entry`, `view`, `share`, `metric` |
| An old app or project address | `/app/<old address>`, `/projects/<old address>` | An address of an old shape, such as `/app/rel1_…` or `/projects/prj_…`, says it names no app or project the person can open, and nothing redirects it. The app's and the project's key opens it. | none |
| Talk it through with AI | `/agent` | Hands an inbox item's prompt to the person's own agent. | `item` |
| AI Tools | `/ai-tools/starters`, `/ai-tools/agents`, `/ai-tools/skills` | Finds a starter, reads Kater's agents and skills, or reads and edits the organisation's and their own, each named by its key: `connection`, `name` and, for a person's own, `user`. Kater's are read-only. | `connection`, `name`, `user`, `view`, `q` |
| AI Tools, History | Any AI Tools tab with `history=open` | Sees who changed what and when in the connection's AI tools and their own `about.md`, the same list on every tab, each file linking to where it is now. | `history`, `change` |
| About you | `/ai-tools/about` | Reads and edits what their agent knows about them and the company. | `doc`, `connection`, `user`, `view`, `history` |
| Theme Builder | `/theme-builder` | Makes and edits a theme for apps. | `theme`, `tab`, `sidebar` |

### The person's own pages

| Page | Address | What a person does there | Keys |
| --- | --- | --- | --- |
| Inbox | `/inbox` | Reads everything Kater tells them: what needs them, updates and what people shared. | `filter`, `done`, `item` |
| Alerts | `/alerts` | Sees each of their alerts and what it says today, changes where it goes, opens its form and removes it. | `alert` |
| Workflows | `/workflows`, `/workflows/overview`, `/workflows/<connection>/<name>`, `/workflows/<connection>/users/<user id>/<name>` | Sees every run Kater did for them by day and what each made, opens one workflow with its instructions and runs, runs it now, and edits it (`edit=1`). A workflow is named by its key: `<connection>/<name>` for one of a connection's AI Tools workflows, `<connection>/users/<user id>/<name>` for a person's own, as `kater workflow list` gives it. | `run`, `details`, `used`, `context`, `step`, `sql`, `edit`, `q`, `new` |
| Trash | `/trash` | Restores what was deleted in the last 30 days, and fixes the objects that read something deleted. | `tab`, `item`, `search`, `type`, `layer`, `ownedBy`, `deletedBy`, `order`, `restore`, `fix`, `purge` |
| Requests | `/requests` | Follows their requests to promote a field into the shared model. | `tab`, `request` |
| Bug reports | `/reports` | Reads, sorts and searches every bug report that people in the organisation sent to Kater; closes, reopens or changes the severity of their own (an admin, of any); opens a report beside the list. | `status`, `sort`, `q`, `type`, `severity`, `by`, `report` |
| Bug report | `/reports/<report id>` | Reads one bug report, such as `BUG-1042`, and confirms its fix. | |
| Profile | `/profile`, `/profile/<user id>` | Their own profile: notifications, their own Slack connection, AI app, agents and links. An admin opens another person's. | `#notifications`, `#ai`, `#agents`, `slack`, `reason`, `remove` |

### Admin

Only an admin opens these pages.

| Page | Address | What a person does there | Keys |
| --- | --- | --- | --- |
| Admin dashboard | `/admin` | Sees how people use Kater: views, what people ask, the answers in AI apps, the AI apps people connected, and what needs attention. | `window`, `staleDays`, `theme` |
| Objects | `/admin/objects` | Lists every object, finds stale ones and those no one manages, reviews promotions, and restores and fixes every delete (`status=deleted`, `status=fix`) and every retired field (`status=retired`). | `q`, `kind`, `owner`, `status`, `object`, `promotion`, `item` |
| Conversations | `/admin/conversations` | Sees whether people get correct answers and what to fix: how the answers stand, What works, What to fix and each conversation with a verdict for each answer, the calls the agent made and the rows behind each figure. | `window`, `q`, `person`, `topic`, `verdict`, `takeaway`, `conversation`, `used`, `context`, `step`, `sql`, `raw` |
| Users | `/admin/settings/manage-users` | Invites, changes and removes the people of the organisation. | `user`, `q`, `new`, `remove`, `cancel` |
| Check access | `/admin/settings/manage-users/check-access` | Checks what one person can read and change, folder by folder. | `user`, `path` |
| Files & access | `/admin/settings/access` | Sees and changes who can open each folder and file. | `path` |
| Attributes | `/admin/settings/permissions/attributes` | Sets the attributes that limit which rows a person reads. | `attribute` |
| User Groups | `/admin/settings/permissions/user-groups` | Groups people, to give a group access. | `group` |
| Organization | `/admin/settings/organization` | Changes the organisation's details, and its bug report settings with `tab=bug-reports`. | `tab` |
| Connections | `/admin/settings/connections` | Lists the warehouse connections, and adds one. | `new`, `tab`, `step`, `connection` |
| Connection | `/admin/settings/connections/<connection>` | One connection's overview: its sign-in, whether connection.yaml matches it, its tables and its last sync, with Sync now and Check for changes. | `choose`, `signin` |
| Connection syncs | `/admin/settings/connections/<connection>/syncs` | The connection's sync schedule and every sync run. | `choose`, `signin` |
| Connection sync run | `/admin/settings/connections/<connection>/syncs/<run>` | What one sync, or Check for changes, changed in the DB datasets and the Kater datasets, and what to do about each problem. | `show` |
| Connection schema | `/admin/settings/connections/<connection>/schema` | Each table with its DB dataset and its Kater dataset. | `show`, `q` |
| Connection settings | `/admin/settings/connections/<connection>/settings` | The connection's connection.yaml. | `view` |
| Repository | `/admin/settings/repository` | Connects the GitHub repository that receives the shared layer. | `action` |
| Integrations | `/admin/settings/integrations` | Lists Slack, with its state. | |
| Slack Settings | `/admin/settings/integrations/slack` | Installs Kater in Slack and sees who connected their account. | `action`, `setup`, `outcome` |
| AI settings | `/admin/settings/ai` | Organisation: the Kater MCP for everyone, with its address and a test prompt; the organisation's Anthropic and OpenAI API keys, added, replaced and removed, and a switch for each feature they run (in-app chat, Slack questions and scheduled workflows), all on with the first key; In-app chat under them: the default model and effort, whether people may select their own, and, with two keys or more, the Anthropic keys that answer there, in order; then this month's use, for every feature or one, and the spend limits. Kater MCP: allows people's AI apps, selects the default provider, and finds the Kater MCP's addresses and how to grant access to Kater MCP in each provider, step by step. | `tab`, `action`, `key`, `provider`, `usage`, `grant`, `step` |
| Scheduled workflows | `/admin/settings/workflows` | Sees every scheduled workflow and Slack schedule in the organisation, with its owner, when it runs, its last run and its last 30 days, and pauses or resumes one. | |
| Settings | `/admin/settings` | A page with its title only. Kater has no settings here yet. | |
| Billing | `/admin/settings/billing` | A page with its title only. Kater has no billing here yet. | |
| All bug reports | `/staff/bug-reports` | Kater's own staff only: every organisation's bug reports. | `status`, `sort`, `q`, `report`, `org` |

## Keys on every page

- `ask=open` opens the Ask panel beside the page, and `ask=full` opens it in the page's place. Kater answers there when an admin selected a key for in-app chat on AI settings. With it, `ask.conversation=<id>` opens one conversation, `ask.history=open` opens History, and `ask.work=<turn>` opens a turn's steps. `kater here` says which conversation a tab shows.
- `new=<kind>` opens the New dialog for an app, dashboard, query, chart, project or workflow, with `new.tab=manual` for Manual.
- `report=new` opens Report a bug over the page: one form, already filled in from an error when Report this bug opened it, and Send.

A key this guide does not name is still read: `kater place <address>` says what an address shows before you open it.
