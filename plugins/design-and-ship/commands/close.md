---
description: Close a design&ship run. Update the final documents, list leftovers, sweep for secrets, commit.
argument-hint: <run folder>
---

Run the **design&ship** close for: $ARGUMENTS

Load the `design-and-ship` skill and follow `prompts/03-close.md` from it exactly: update `DESIGN.md` so it matches reality, turn recurring operations into a RUNBOOK/PLAYBOOK/ADR only where warranted, sweep the documents for secrets, list leftovers with owner, write the Lessons section, and commit only the files you touched with an explicit pathspec.
