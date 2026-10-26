# Worked example: cutting over two mail-triggered workflows between cloud subscriptions

A real design&ship run, told so you can see where each phase paid off. All names, ids and addresses were removed. The numbers (counts, times) are the ones observed in the run.

## The problem

A company had two workflows (Logic Apps style) that **fire when an email arrives** in two mailboxes. Each reads the attachment, calls an HTTP function, and that function stores rows in a database. They lived in an old ("legacy") subscription. The new infrastructure (a landing zone governed as code) already had copies of those two workflows and of their function, but **switched off** and pointing at a subscription that no longer existed.

The job: **move the processing from the legacy subscription to the new one**, on a low-traffic Saturday, without losing or duplicating a single email.

The human's constraint, verbatim and decisive: *no interactive authorization of the mailbox connections is possible* (it required a second factor on a phone he did not have). So the mailbox connections **had to survive** the change.

## Phase 0 - Anchor

Before measuring anything, the work was recorded in a ticket and a log line. It is little, but it prevents work "on the fly" that nobody can find later.

## Phase 1 - Measure

Read-only agents measured in parallel. Each delivered an input with its verdict. Findings, all **MEASURED**:

- The legacy and the new workflow definitions were **identical**, except for the function URL. (Checked with a `diff`, not by eye.)
- Both functions read **the same connection-string secret**, so they pointed at the same database. (Equality of the *value* was marked INFERRED: only the secret's name was compared.)
- A connection **cannot be referenced from another subscription**: the deployment fails with a "connection under wrong subscription" error. So the connections had to be **moved** to the new subscription, not reused from afar.
- There was already a precedent: those same connections had crossed subscriptions months earlier and were still connected. That suggested that moving a resource keeps the authorization token (the current state was MEASURED; that it had been a "move" was INFERRED).
- The move validation (`validateMoveResources`, read-only) returned 204, i.e. valid.
- **Key finding:** when a new workflow is enabled, the mail trigger **re-scans the mailbox** and reprocesses old mail. It had been seen before: 45 of 75 runs in one minute. Neither the workflow nor the function was idempotent, so a reprocess **duplicates orders and inventory**.
- An existing work branch already tried to solve it with a date filter, but it was **broken**: it used a parameter that was not declared in the workflow definition. The platform accepts it on save, and **every run fails**. It was measured with a throwaway workflow in dev (later deleted).
- With the parameter declared and comparing `ticks()` of dates, the filter worked in five tested cases: +1 s passes, -1 s is skipped, equal is skipped, a month ago is skipped, tomorrow passes.
- The legacy workflow already failed one run in three, because of **business errors from the function** (409, 422, 400). That is prior behavior, not a regression of the cutover.

## Phase 2 - Unify: Design Doc + Runbook

The Design Doc made one design decision and defended it with evidence:

- **Move** the connections (with their token) to the new subscription, next to their workflows, instead of creating new connections.
- **Fixed cutoff filter**: an `emailCutoffDateTime` parameter declared in the workflow, defaulting to the year 9999 (blocks everything). When the new workflow is switched on, the cutoff is set to the **exact time the legacy one was switched off**. Anything earlier was processed by the legacy one and is discarded even if the trigger re-scans the mailbox; anything later belongs to the new one.
- **Switch on from the command line, not from a pipeline**: a single write that sets the state and the cutoff together. A pipeline adds queue, stage and approval, i.e. minutes during which the mailbox has no owner.
- Golden rule: **never both workflows enabled on the same mailbox**.

The Runbook had three phases, each step with action, gate, criterion, positive control and rollback:

| Phase | What | Key gate |
|---|---|---|
| 1. Move (legacy keeps processing) | Backup, move resources between subscriptions, check connections are still `Connected` and a connection test returns 200, repoint the legacy workflow to the moved connection | **Token gate:** if the connections are not still connected, STOP and roll back phase 1 |
| 2. Repoint the new one as code (still off) | Commit with the fixed filter and the moved connection, deploy, `what-if` to prove only one resource changes | Approve the deployment only if the run is the expected one (one stage, one resource, right branch and commit) |
| 3. Cutover | Per flow: switch off the legacy one and note the time `t0`; switch on the new one with cutoff `t0`; align the repo with what is live; observe | Success = at least one real run per flow with the HTTP call green |

The rollback criterion distinguished **platform failure** (trigger, connection, 5xx, timeout, DNS, filter failure): immediate rollback, from **business error** of the function (409, 422, 400): recorded and compared with the legacy one; only two or more in a row with no success trigger the rollback. The cutover rollback re-enabled the legacy workflow **with the same cutoff filter**, so as not to duplicate what the new one had already processed.

That filter was also recorded as a permanent leftover: until the function is idempotent, **every future redeploy must raise the cutoff to the deploy time**.

## Human GO

A short list of decisions with recommendations was consolidated. The human answered in writing and asked to bring the cutover forward to that same Saturday "when you see everything works well", rather than wait for the previously planned date.

## Phase 3 - Execute in one go

- A report per phase in the EXECUTION-LOG. Gates respected. Approximate times were marked as approximate and later corrected in a separate commit, because a time is data and is not embellished.
- Phase 1 passed the token gate. Phase 2's `what-if` showed exactly one `Modify`, zero `Create` and zero `Delete`.
- At the cutover, the gap between switching off the legacy one and switching on the new one was seconds.
- An end-to-end acceptance test of one flow turned green that same afternoon. The other was marked **pending its first real email**, not "ok".

## A second round, also long design

Days later, with the same method, another round decided what to do with the remaining legacy resources. What showed the value of measuring:

- A resource that the original brief called "legacy" turned out, once measured, to be the alarm clock of another system: it was **kept** and left out of scope.
- A user-facing static app was still calling the legacy function. The new function turned out to be an **exact superset** (same 10 endpoints plus one, same total rows on a control endpoint: 9329 = 9329). The proposal: **repoint first, wait at least one business day of "soak" with zero executions on the old function, and delete last**. The order "repoint, verify, soak, delete" is the rollback.
- A zero measured on a Sunday **does not prove** nobody uses it: it was written down that way.
- Deletions stayed as a **proposal with explicit GO**, with the pre-checks listed.
- An honest correction: the first round's design claimed that removing a deployment parameter was the fix; reading the task's documentation, the parameter was **mandatory** in that scope. The design was corrected to "one variable per environment", with the source cited. A design that is wrong and gets corrected in time is the method working.

## What this example teaches

1. **Measuring changed the plan.** The finding about the mailbox reprocess and the broken filter redirected the whole design. Without that phase, a filter that made every run fail would have gone to production.
2. **Evidence over judgment.** `diff` of definitions, counts, five-case tests, `what-if`: no decision rested on "it seems".
3. **Rollback per step**, and a gate (the token one) with its rollback already written.
4. **Deletions wait.** First repoint, then measure, then propose, and only with GO delete.
5. **Label what was not measured.** More than one claim in the design says INFERRED, and that is fine: the point is not to disguise it as fact.
6. **Execution was short because design was long.**
