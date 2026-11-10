---
name: diagnose-findings
description: "Run an analysis's declared follow-up queries on its material findings. A step of deep-analysis."
---

This skill is Kater's guide `vfs://guides/diagnose-findings.md`.

# Diagnose findings

Run declared follow-up contracts to test plausible drivers for bounded material findings.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

## Procedure

1. Confirm the diagnostic branch is declared, its upstream decision recorded bounded finding keys, and its follow-up query contract and parameters exist in the selected workspace.
2. Invoke the exact diagnostic contract with only those approved finding keys and declared parameters. Record its run identity, truncation, returned fields, and concise evidence linked to each finding key.
3. State what the diagnostic distinguishes and what remains uncertain. Label every explanation the evidence doesn't separate a hypothesis.
4. Mark the diagnostic node complete only after its governed run and evidence are recorded. Route to verification, not to an improvised next query.
