---
name: test-model
description: "Lock in trusted answers as tests of a shared model, and run them. Use it when the person asks: test a model, keep these answers right."
---

This skill is Kater's guide `vfs://guides/test-model.md`.

# Test a model

Turn answers the person trusts into tests, so a later change that moves one fails loudly. Read vfs://guides/tests.md first: it gives the test file's shape and rules.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## When to use

Use this when the person wants to trust a model, before or after a change to it, or after a `/check` held. Their tests of a shared model go in their own `connections/<connection>/users/<email>/tests/`, each naming the model in `model: <kind>.<name>`; the shared layer's file is only for someone who can edit the shared models and asks for it.

## Procedure

1. **Pick the model:** the `model` input, or the model behind the metrics in their About you. Read what's tested already with `kater test list REF`.
2. **Plan the suite.** `kater test generate REF --dry-run` shows the tests it would draft, one of each shape, and `--from-questions` adds one for each business question no test answers yet. An answer the person already trusts, such as a figure from a report, becomes its own test too.
3. **Ask once.** Show the plan. After a yes, draft it in their tests file with `kater test generate REF`, and their own answers with `kater new test`.
4. **Check each draft with them.** Run the model's tests with `kater test --select REF`, and show each test's answer beside what it claims. Only the person can say an answer is right, from a source they trust or a `/check`. Mark each one they confirm with `kater test update REF NAME --checked`, and leave the rest as drafts, saying why.
5. **Report.** One line, such as "6 tests, 5 checked, all passing", then the run. Name what's still untested; `kater test readiness REF` says whether the model is ready to hand off.

## Stop conditions

- A checked test fails: it's a finding, not something to re-record. Report it, and offer `/check` on the number.
