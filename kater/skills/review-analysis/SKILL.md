---
name: review-analysis
description: "Check a finished analysis before its findings are reported. Use it when the person asks: review my analysis, check the work."
---

This skill is Kater's guide `vfs://guides/review-analysis.md`.

# Review an analysis

Independently validate a completed analysis artifact before findings are reported.

**Lessons.** When `kater ai-tools list` names `lessons` for this skill, read that file before you start and follow it: it is what this organisation learned using this skill. Record a new lesson about using it there, as vfs://guides/lessons.md says.

Started by the person, as `/review-analysis`, check the analysis they name, else their latest analysis note in their brainstorm folder (`kater scratch list --json` gives each topic's status), in a fresh context. Tell them the disposition in one line, then the evidence.

## Checklist

1. Confirm the resolved workspace identity, access status, selection reason, timestamp, and catalog/compiler revision still match the persisted state.
2. Confirm every DAG object is built or validly pre-existing in the selected workspace, and its compiled identity matches the artifact.
3. Confirm each invocation parameter is declared, each unknown parameter has profile evidence and a recorded decision, and autonomous choices name an authorized policy and provisional status.
4. Confirm every complete node has a successful named tool call, run identity, bounded evidence, and non-truncated or appropriately caveated result. Confirm diagnostics used only declared, bounded finding keys.
5. Confirm the DAG reaches verification and report readiness, and every proposed numeric claim maps to a returned run field. Do not repair unsupported claims.

## Output

Write `## Review` with one disposition: `pass`, `pass-with-caveats`, or `blocked`. For caveats or blocks, name the exact node, evidence gap, and retry or remediation action.
