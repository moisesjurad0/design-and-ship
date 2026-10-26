---
description: Start the design phase of design&ship for a change. Read-only measuring, then DESIGN.md + RUNBOOK.md, then STOP for the owner's GO.
argument-hint: <name of the change and its goal>
---

Run the **design&ship** design phase for: $ARGUMENTS

Load the `design-and-ship` skill and follow `prompts/01-design-phase.md` from it exactly:

1. Name which method applies (direct / design&ship / spec method) and why, in one line.
2. Ask at most one question at a time if the goal, constraints or run folder are missing.
3. Create the run folder with `inputs/`. Measure read-only. Label every claim MEASURED or INFERRED.
4. Write `DESIGN.md` and `RUNBOOK.md` from the skill's templates. Check the "design finished" rule.
5. Deliver at most 15 lines with the open decisions and your recommendations. Commit only the run-folder files.
6. STOP. Execute nothing until the human gives the GO.
