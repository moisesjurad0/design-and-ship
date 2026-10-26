---
description: Execute an approved Runbook in one go, with a phase report per phase. Only after the owner's GO.
argument-hint: <run folder> [decisions and authorizations]
---

Run the **design&ship** execute phase for: $ARGUMENTS

Load the `design-and-ship` skill and follow `prompts/02-execute-phase.md` from it exactly.

Before anything else, confirm the human has given an explicit GO with the decisions and the exact scope of what is authorized. If there is no GO, or the scope is unclear, ask one question and stop.

Then: read-only pre-flight, backup, run the Runbook in order, stop only at declared gates, append a phase report to `EXECUTION-LOG.md` after each phase, and finish with a final verification and the explicit leftovers. Do not do the close yet.
