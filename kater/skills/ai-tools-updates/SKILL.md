---
name: ai-tools-updates
description: "What changed in the shipped AI tools. Use it when the person asks for: \"What is different between the shipped text and ours?\", \"give me the diff\", keep ours or take the shipped text, or an inbox row \"There is newer text for …, which your team edited\"."
---

This skill is Kater's guide `vfs://guides/ai-tools-updates.md`.

# What changed in the shipped AI tools

The agents, skills and workflows in each connection's `ai_tools/` ship with each release, and their shipped text changes from release to release. Each night every file that nobody edited takes the newer shipped text, by itself. A file the organisation edited keeps its edit. When the shipped text of it changed too, the file waits for a person: each person who can change it gets one inbox row, such as "There is newer text for skills/compare.md in sales, which your team edited. Ask your agent what changed." The portal shows the file and its History, never a diff or a decision. You answer the rest.

## Answer "what is different from the shipped text?"

1. Run `kater ai-tools status --connection CONNECTION`. Each row that waits has its `kind` and, in `choices`, what `keep` and `take` do. `who` names who can change them when the person cannot.
2. Run `kater ai-tools diff PATH --connection CONNECTION` for each file that waits, with PATH as status gives it, such as `skills/compare.md`. `yours_diff` is the organisation's change and `kater_diff` is the shipped change, each from the text both started from (`base`).
3. Show both diffs as diffs when the person asks for the diff. Then say in one line each what the team changed and what the shipped change is.
4. Recommend one answer, with its reason, and change nothing until the person says yes.

| `kind` | What happened | `keep` | `take` |
| --- | --- | --- | --- |
| `conflict` | The team edited it, and the shipped text changed too | Keeps the team's text. The question comes back only when the shipped text changes again | The text that ships now. The team's edit is lost |
| `base-unknown` | It matches no shipped text | Keeps it as the team's, on top of the text that ships now | The text that ships now |
| `moved-edited` | The item moved, and the team edited it | Keeps it where it is, as the team's. An old name with `-` is kept at its name with `_` (`workflows/deep-analysis/` at `workflows/deep_analysis/`), which `choices` names; with no `keep` in `choices`, that name is taken, so only take answers | Moves it, with the team's edits |
| `gone-edited` | The item no longer ships, and the team edited it | Keeps it as the team's, at its name with `_` when its name has a `-` | Removes it |
| `names-old-path` | A file outside `ai_tools/` names an item that moved or no longer ships | Not a question: edit the file to name the new path, or take the old one out |

## Act after the yes

Run each with `kater_apply`, since the `kater` tool refuses a write:

- Keep ours: `kater ai-tools update --connection CONNECTION --yes --decide PATH=keep`.
- Take the text that ships now: the same with `PATH=take`.
- Never ask again about this file: `kater ai-tools keep PATH --connection CONNECTION`. Offer it only when the person says so.
- Both: save the merged text in the file, then answer `--decide PATH=keep`.

Each answer is one commit by the person, named for what it did, such as "Keep your skills/compare.md in sales". The History button on AI Tools lists it, and its Undo takes it back. The inbox row closes when nothing waits.
