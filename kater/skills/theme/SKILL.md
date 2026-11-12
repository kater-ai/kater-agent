---
name: theme
description: "Themes. Use it when the person asks for: How an app or a dashboard looks: \"make the theme warmer\", a new theme from a description, or a change to the theme open in the Theme Builder."
---

This skill is Kater's guide `vfs://guides/theme.md`.

# Themes

A theme is how an app or a dashboard looks: its colours in light and dark mode, its chart colours, its fonts, its corner radius, its spacing and its shadow. Use this guide when the person asks to change how something looks, to tweak the theme they have open, or to make a new theme from a description, such as "a calm navy theme with a serif font for the board deck".

## What a theme is

Every theme builds on one of Kater's presets and stores only the tokens it changes. Everything else resolves from the preset when the app or the dashboard draws, so a theme keeps getting the preset's fixes and new tokens. Never copy a whole preset into a theme: write only the tokens that differ.

The presets are `kater`, `shadcn`, `claude`, `solar-dusk`, `modern-minimal`, `seagreen`, `caffeine`, `darkmatter`, `graphite`, `vercel`, `sage-garden`, `twitter`, `corporate`, `notebook`, `mono-currency`, `spring` and `journal`. Start a new theme from the preset nearest to what the person describes, so it changes few tokens.

A theme is written in one of three places:

| Where | File | Who sees it |
| --- | --- | --- |
| A saved theme of the person's own | `themes/{name}.yaml` in the person's folder | The person. It shows in the Theme Builder's list. |
| A saved theme of the data team, for everyone | `vfs://connections/{connection}/themes/{name}.yaml` | Everyone. An app or a dashboard names it as `team:{name}`. Only a person the connection lets save team themes can write it. |
| One app's or one dashboard's own theme | `application.theme` in the app's `kater.app.yaml`, or `theme` in the workspace's `dashboard.yaml` | That app or that dashboard. |

`{name}` is lower snake case, as every Kater object's name is: it starts with a lowercase letter and has at most 64 lowercase letters, numbers and underscores, such as `board_navy`. The Theme Builder makes it from the theme's label, with `_2` when the name is taken. Only Kater's presets keep their hyphens, such as `solar-dusk`. A `kater` command names a theme by its key: `<connection>/<name>` for the data team's, `<connection>/users/<user_id>/<name>` for the person's own, or a preset's own name. To give a saved theme another name, rename its file in Files or with `kater rename`: every app, dashboard and theme that names it follows.

A saved theme file:

```yaml
label: Board navy
preset: corporate
overrides:
  colors:
    light:
      primary: '#1f3a68'
      primaryForeground: '#ffffff'
    dark:
      primary: '#9db8e8'
      primaryForeground: '#0b1424'
  typography:
    fontFamily: "'Source Serif 4', ui-serif, Georgia, serif"
  borderRadius: 0.25rem
```

A person's saved theme may build on a team theme (`preset: team:shoptech_brand`). A team theme builds only on a Kater preset.

In an app's manifest or a dashboard, the theme is either a name, a preset such as `spring` or a team theme such as `team:shoptech_brand`, or a mapping with `preset` and `overrides`, written as above with no `label`. An app or a dashboard with no theme wears the `kater` preset.

## The tokens

`overrides` takes only these groups. A name that is not in the list is an error.

- `colors.light` and `colors.dark`: `background`, `foreground`, `primary`, `primaryForeground`, `secondary`, `secondaryForeground`, `accent`, `accentForeground`, `destructive`, `destructiveForeground`, `border`, `input`, `ring`, `muted`, `mutedForeground`, `card`, `cardForeground`, `popover`, `popoverForeground`, `sidebar`, `sidebarForeground`, `sidebarPrimary`, `sidebarPrimaryForeground`, `sidebarAccent`, `sidebarAccentForeground`, `sidebarBorder`, `sidebarRing`, and the chart palette `chart1` to `chart5`.
- `chartColors.light` and `chartColors.dark`: `positive`, `negative`, `warning`, `grid`, `axis`, `gauge_track`, and `series`, a list of exactly three colours. The `kater_*` tokens are the colours of Kater's object marks: leave them as the preset has them. The Shared layer's objects wear `positive`, the green of Verified, so a change to `positive` changes them too.
- `typography`: `fontFamily`, `fontSerif`, `fontMono`, `fontSizeBase` (such as `15px`), `fontWeight` (such as `400`) and `letterSpacing` (such as `0em`). A font family must be one that comes with Kater or a system font stack.
- `shadow`: `light` and `dark`, each with `color` and `opacity`, and `blur`, `spread`, `offsetX` and `offsetY`.
- `borderRadius` and `spacing`: one CSS length each, such as `0.5rem`.

Each value is a short CSS value, such as `'#1f3a68'` or `oklch(0.45 0.1 260)`, never a selector, a declaration or a `url()`. Quote a hex colour in YAML.

Change a colour in both modes, and change each foreground with its background. Every pair you change must keep a contrast of at least 4.5:1, such as `primary` with `primaryForeground`, `background` with `foreground` and `card` with `cardForeground`. `positive`, `negative` and `warning` must stay different from each other.

## Find what the person has open

`kater_start`'s `kater` says the person's front tab. Its address names the theme to change:

- `/theme-builder?theme={theme}`: the Theme Builder, on a preset's name, a saved theme's id, or `team:{id}`. Change that saved theme's file. When `theme` names a preset, make a new saved theme that builds on it, since a preset never changes. Edits the person made in the builder and did not save are not in the address, so ask about them when the person mentions them.
- An app with its sidebar on `theme` while editing: the app's `kater.app.yaml`, `application.theme`.
- A workspace's Dashboard with `mode=edit` and `dashboard_sidebar=theme`: the workspace's `dashboard.yaml`, `theme`.

Read the file first. When an app or a dashboard names a saved theme, ask the person whether to change that theme, which changes everything that uses it, or to give this one its own theme with `preset` set to that name and only the new tokens under `overrides`.

## Make or change a theme

1. Read the file you change, or the preset you build on, to see the tokens it has now: `kater invoke source.read --request-file -` with `{"path": "PATH", "structure": true}` shows a file's values and its problems.
2. Write only what changes. Change one property in place with `kater source patch PATH --edits JSON`, such as `[{"at": "overrides.colors.light.primary", "value": "#1f3a68"}]`, or write a new saved theme whole with `kater source write PATH --text-file -`. A theme is outside the person's brainstorm folder, so through the MCP run each with `kater_apply`, once the person has asked for the change or said yes to it.
3. Read the save's `problems`. Each names its token, such as `overrides.colors.light.foreground`, and says what is wrong: an unknown token, a value that is not a token, or a pair that lost its contrast. Fix every one before you answer.
4. To put a saved theme on an app, set the app's `application.theme` to its name: the id for the person's own, `team:{id}` for the team's.

The Theme Builder, the app and the dashboard read the change at once, so the person sees it on the page they have open. Tell the person what you changed, in words, such as "a navy primary colour and a serif font".
