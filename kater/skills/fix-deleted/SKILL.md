---
name: fix-deleted
description: "Delete, restore and fix what reads something deleted. Use it when the person asks for: \"Delete this\", \"bring it back\", \"what is broken\", or a fix of what reads something deleted, from a prompt that Copy prompt for your agent copied."
---

This skill is Kater's guide `vfs://guides/fix-deleted.md`.

# Delete, restore and fix what reads something deleted

Every delete in Kater goes to the Trash for 30 days, from every place: the portal, Files, `kater rm`, a push, an agent and VS Code. A delete is one change. The object goes with its own parts, such as a query's charts, and each object that reads it stays. Kater changes each part of those objects as the plan says: Remove, Replace with another object, or Leave as is, which shows Not available until someone fixes it. Restore brings the delete back. Needs a fix in the Trash lists each deleted object that something still reads, with Kater's fix.

You run the same plans as the portal's dialogs, with the `kater` tool. Each command has `--dry-run`, which prints the plan and changes nothing. Run each command without `--dry-run` only with kater_apply, and only after the person says yes. The `kater` tool refuses it.

## Fix what reads a deleted object

Copy prompt for your agent, in the Trash, gives you a prompt that names the deleted object, its Trash id and Kater's fix, and the command to run. Do these steps in order:

1. **Run the command with `--dry-run`.** For example, `kater trash fix 3f1c9a20-5b7e-4d21-9a0e-1c2d3e4f5a6b --dry-run`. Keep each option that the prompt's command has: those are the choices the person made in the review dialog.
2. **Read what it prints.** First the verdict: Ready to fix, Check before you fix, or Not quite ready to fix yet. Then Kater's fix in one sentence, why Kater selected what takes the deleted object's place, and Replace it with. Then each object to fix, by layer and type, and each part with its fix, its id after `--part`, and its code before and after: `Before:` is the line as it reads now, and `After:` is the line as the fix leaves it, or `Line removed`.
3. **Check each part.** Read the object's file where you need more than the line, with `read_file`. A part is correct when the after reads well where it is: a sentence still says something true, a chart still has its fields, a query still compiles. Where a fix is not correct, change it: `--part <id>=remove`, `--part <id>=replace` or `--part <id>=keep`. To put another object in the deleted one's place, give `--with <name>`, one of the names that Replace it with lists, or `--with none` to remove each part in place of a replace. Run `--dry-run` again after each change.
4. **Tell the person what will change, and wait.** Say how many objects change, whose work they are, what takes the deleted object's place and what still shows Not available. A replacement whose figures differ needs the person's yes: the verdict says Check before you fix and gives the reason, such as "So each figure can be lower." Say that reason in your own words. Do not run the fix until the person agrees.
5. **Run the same command without `--dry-run`, with kater_apply.** It makes one change. A fix of other people's work runs as a job: the command says it is not written yet, and gives its change. Tell the person the change id: `kater undo <change>` and Undo in the Trash take it back. Kater tells the owner of each object it changed, in their Kater inbox.

When the verdict says Not quite ready to fix yet, no fix is set for a part, such as a sentence that the next sentence follows from. Read the part, suggest a fix to the person, and give it with `--part` once they agree. Never select a fix for the person without telling them.

## Fix a reference that a change broke

A reference also breaks with no delete behind it: a change takes a field out of a query, or Kater has lost a kept figure. `kater trash list --fix` lists each one with its word, who changed it and when, each file and line that reads it, and Kater's fix. `kater check references` lists the same and exits 1 when there is anything on the list. Only these two lists name who changed it: never name that person anywhere else.

1. **Run `kater trash fix <id> --dry-run`.** Without `--how` it takes Kater's fix: Replace, or Put back for a field, a chart or a query that a change took away. `--how replace --with <name>`, `--how put-back`, `--how remove` and `--how keep` select another.
2. **Tell the person what will change, and wait.** Then run the same line without `--dry-run`, with kater_apply. Kater makes one change, and `kater undo <change>` takes it back. Put back never writes what does not compile: when it refuses, nothing changed, and its words say what to do next.

A figure that says Not kept has no answer to read. For a reader who may change the entry, Read it again on the figure reads it now and says the day. `kater trash fix <id> --how remove` removes the figure. On a metric's baseline it removes only the baseline, and the metric stays.

When Kater first checks the references, only an Admin sees the breaks from before. With an Admin, fix or delete what they want first. Then run `kater trash send-owners` with kater_apply, once they say yes: each owner gets one row in their Kater inbox.

## Delete

- **`kater rm <path> --dry-run` first.** It prints the Delete dialog: Will affect, by layer, whose work it is and who used it in the last 30 days; the verdict (Safe to delete, Check before you delete, or Not quite ready to delete yet); what goes with it; and Review what happens to each thing, with each part's fix, its id and its code before and after. Under each affected object it names what reads it in turn, such as an app or a dashboard.
- **A view count is not a reason.** "No views in the last 30 days" says only that nobody opened it in Kater. A brief can still go to people on a schedule. Give the person the count, and never say that nobody relies on an object because of it.
- **Name an object by its path.** A query's or a chart's `.sql` file, a workspace's, an app's or a project's folder, a model's `.sql` file, or `<model file>#<field>` for a field of a model.
- **Change the plan as the dialog does.** `--replace-with <name>` puts another object in its place, `--part <id>=remove|replace|keep` changes one part, `--pick-anyway` selects Kater's guess where it could not decide, `--delete-too <path>` sends one of the person's own objects that read it to the Trash with it, and `--tell` tells the other people who used it in the last 30 days, in their Kater inbox.
- **A field or a model of the shared layer can be retired instead.** When the delete would leave a part showing Not available, the plan says "Or retire it instead". `kater retire <path>` keeps it working for what reads it, and hides it from new work. It has no `--dry-run`: the delete's plan already says what reads it, and `kater undo <change>` takes a retire back. It does not go to the Trash. `kater retire <path> --use-again` takes Retired away.
- **Tell the person the plan and wait.** Then run the same line without `--dry-run`, with kater_apply. It prints the Trash id, `kater restore <id>` and `kater undo <change>`.

## Restore

- **`kater trash list`** lists what the person can see in the Trash, each row with its id. `kater trash list --fix` lists Needs a fix: what reads something deleted and every other reference that broke.
- **`kater restore <id> --dry-run`** prints the Restore dialog: what comes back, what reads it again, each fix made since the delete with its id after `--keep`, and what changed since. A restore takes back each fix unless you keep it with `--keep <id>`. When a newer object took its name, give it another name with `--name <name>`. When its folder was deleted too, the folder comes back with it.
- **Tell the person what comes back and what each kept fix means, and wait.** Then run the same line without `--dry-run`, with kater_apply.

When the person cannot restore an object, the Trash shows who can, and the person asks them with Ask in the Trash.
