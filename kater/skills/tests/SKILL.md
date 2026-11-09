---
name: tests
description: "Tests of shared models. Use it when the person asks for: A check that a shared model still gives the numbers it should."
---

This skill is Kater's guide `vfs://guides/tests.md`.

# Tests of shared models

A test is a fixed question with a fixed answer: these fields from this model, with these operations, these static filters, this date range and this limit, return exactly these rows. Write a small suite once a semantic model or Kater dataset is done, check its numbers against something you trust, and run it whenever the model, its upstream, the SQL writer or the compiler changes. A workspace can have tests of its own, of its root's parameters and filters. Anyone can keep their own tests of a shared model in their own folder.

## The steps

1. **Write the tests:** the file, its keys, its expectations, its dates and its fixtures, as [writing a test](vfs://guides/tests-writing.md) says.
2. **Start a suite, record its answers, run it and hand it off:** [starting, recording and running](vfs://guides/tests-running.md).
