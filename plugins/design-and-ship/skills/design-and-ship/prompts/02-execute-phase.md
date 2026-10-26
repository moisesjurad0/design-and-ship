# Prompt 02 - Execute phase (GO with decisions, report per phase)

Use only after you reviewed `DESIGN.md` and `RUNBOOK.md` and answered the open decisions.

````text
design&ship: GO. EXECUTE the RUNBOOK of [run folder] in a single batch.

DECISIONS (my answers; they replace the DESIGN recommendations where they differ)
- D1: [answer]
- D2: [answer]
- D3: [answer]
AUTHORIZATIONS (exact scope)
- I authorize: [concrete actions, e.g. "Runbook steps 1.0 to 3.5, including the deletions in section 5 rows A and B"].
- I do NOT authorize: [everything else; in particular any deletion not listed above].
- Window: [date and time, time zone].

HARD RULES
1. Execute ONLY what the Runbook says, in its order. What is not written is not done. If something else is needed, STOP and report it.
2. Before step 1: read-only pre-flight. Re-confirm the measured AS-IS is still true. If something changed since the design, STOP and report it.
3. Backup (no secrets) of everything you will modify or delete, in the run folder, BEFORE doing it.
4. Every command explicitly names its target (account, subscription, environment). Never use defaults.
5. Stop at the declared human gates and nowhere else. At a gate: one single question, with the evidence and your recommendation; wait.
6. Each step meets its criterion AND its positive control before moving to the next. A check that could not run is NOT a passed check: report it as "not measurable".
7. If a rollback criterion is met, execute the Runbook's rollback without asking permission and report it. If a rollback fails, STOP.
8. Never two active processors on the same input. If a backlog could be reprocessed, apply the cutoff filter defined in the Runbook.
9. Label every claim in the log MEASURED (with command) or INFERRED. Approximate times are marked as such.
10. Notify nobody outside this conversation. Print no secrets.
11. Deletions outside what I authorized above are NOT executed, even if the evidence looks clear.

REPORT PER PHASE
At the end of EACH phase, before the next, append to EXECUTION-LOG.md a "Phase report" block (template in templates/) with: done, MEASURED evidence, deviations, gates, backups, inferred leftovers and decision (continue, stop, roll back). Then send me a summary of 5 lines at most and continue, unless there is a gate or a failure.

WHEN FINISHED
- Final verification against the DESIGN acceptance criteria (met / not met / not measurable).
- List of explicit leftovers with owner.
- Do not do the documents close yet: that is prompt 03.
````
