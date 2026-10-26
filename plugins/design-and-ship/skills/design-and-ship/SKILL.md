---
name: design-and-ship
description: Use for infrastructure, operations, migrations, cutovers, deletions, cloud or CI/CD changes, and any work that crosses several repos or systems, or anything irreversible, costly or production-facing. Also when the user says "design&ship", "design and ship", "plan this migration", "write a runbook", or asks to change many interdependent things. Measures first, writes a Design Doc and a Runbook with rollback per step, waits for the owner's GO, then executes in one go with an execution log. Do NOT use for trivial reversible fixes (use direct execution) or for new code with unclear requirements (use spec-driven development).
---

# design&ship: design long, ship short

Spend the time on design. Execution is clean, in one go, and as short as possible.

Doing risky work "item by item from the chat" loses dependency order, hides contradictions until the most expensive moment (mid-apply) and leaves no trace. This method inverts the split: almost all effort goes to **measuring and designing**, a human **approves concrete decisions**, then you execute one whole batch, stopping **only** at the human gates the design declared.

## Step 0: say which method applies, by name

Before touching anything, apply this test to each item:

> Can I write **now**, in one line, the correct result **and why** it is correct, **and** is the error reversible?
> - Yes to both: **direct execution**.
> - No to either: a dedicated plan that **measures first**. That is design&ship.

| Situation | Use |
|---|---|
| Trivial fix, reversible, one line + its test | Direct |
| Infra, ops, migrations, cutovers, deletions, multi-repo | **design&ship** |
| New code with substantial requirements ambiguity | SDD or another spec method (its `design.md` and `tasks.md` play the Design Doc and Plan roles) |

Tell the user in one line which applies and why. If it is design&ship and they did not ask for it by name, propose it before starting.

## The phases

```
0 Anchor -> 1 Measure, plan per item -> 2 Unify (Design Doc + Runbook)
         -> human GO on decisions -> 3 Execute in one go -> 4 Close
```

| Phase | Who | Produces |
|---|---|---|
| **0 Anchor** | Lead + human | The work is recorded somewhere durable (ticket, note, issue) before starting. No working on the fly. |
| **1 Measure** | Agents, read-only, in parallel | One *input* per item in `inputs/`: what really exists (AS-IS **measured**), what is proposed, what can go wrong. Declares: touches / depends on / blocks / honest verdict. |
| **2 Unify** | Lead | `DESIGN.md` (AS-IS, TO-BE, decisions, deletion proposal, open questions) and `RUNBOOK.md` (ordered steps; each with **action, gate, criterion, positive control, rollback**). Cross-item contradictions are caught and resolved here. |
| **GO** | Human | Answers the consolidated question list and approves the Runbook. Nothing executes without it. STOP here. |
| **3 Execute** | Lead + agents | The Runbook runs in one batch, one **phase report** per phase in `EXECUTION-LOG.md`. Stops only at declared human gates or on failure. |
| **4 Close** | Lead | Final documents updated, log closed, explicit leftovers, commit and push. |

### "Is the design finished?" (falsifiable)

Finished **iff** all three hold:

1. The Runbook can run **without asking the human anything**, except at its declared gates.
2. **Every step** has: **action, gate, criterion, positive control, rollback**.
3. **Every contradiction between items** is resolved **with evidence**, not by eye.

If one fails, do not execute: go back to phase 2.

### Parallelism

- Two jobs are parallelizable **iff** the intersection of what each one **writes** is empty. Otherwise serialize.
- Work that **writes files** goes in its own isolated copy (e.g. a `git worktree`) and is integrated at the end. Read-only measuring can share the main tree.

## Hard rules (each one came from a real failure)

1. **Measure before asserting.** "Doesn't exist", "is broken", "is zero", "nobody uses it" need a direct measurement with the command in view. A truncated or empty listing is not proof of absence: confirm the query reached what you think it reached.
2. **Label every claim MEASURED or INFERRED.** MEASURED carries its source (command, file:line). INFERRED is reasoning without observation. **Never promote an inference to fact**, neither when summarizing nor when reporting.
3. **Ask the real system, not a proxy.** Counting mentions in text does not count writers; reading a router's structure does not prove the contract. If a test "lies", mutate the code and see whether it goes red.
4. **Proposal before deletion.** Every deletion enters first as a proposal in the Design Doc, with evidence it is unused. It runs only with the human's explicit GO.
5. **Backup before anything destructive.** Save the prior state (no secrets) in the run folder before deleting, moving or overwriting.
6. **Rollback per step.** A step with no written rollback is not ready. If it truly has none, it is a human gate.
7. **One question at a time.** When blocked, ask a single question with your recommendation and wait. Re-read the original request first: it is often already answered.
8. **One writer per resource.** Never two agents writing the same thing. Never two systems processing the same input at once (e.g. two triggers on one mailbox).
9. **Cut with a time filter when replay is possible.** When replacing one event processor with another, define a cutoff instant and discard everything earlier: a new trigger can reprocess a backlog and duplicate effects.
10. **Do not notify third parties unless asked.** The human decides who is told what.
11. **Ask for the exact authorization, not the command.** The human approves an action with its scope (what, where, when). Approval does not extend to other plans or batches.
12. **No secrets in documents.** Secret names yes, values never. Do not print or copy state files that hold them.
13. **Fail red, not green.** A check that could not run is not a passed check. "No data" is reported as "not measurable", not "ok".
14. **On contradiction or ambiguity, stop and report it.** Do not pick.
15. **A suite that turns greener is a warning.** If a change makes previously failing tests pass, verify it did not stop measuring.

## How to run it

1. **Design** (read-only, then STOP): follow `prompts/01-design-phase.md`. Create the run folder (e.g. `plans/YYYY-MM-DD-name/` with `inputs/`). Write `DESIGN.md` and `RUNBOOK.md` from `templates/DESIGN.md` and `templates/RUNBOOK.md`. Deliver a summary of at most 15 lines with the consolidated open decisions, each with your recommendation. Commit only the run-folder files with an explicit pathspec. **Stop and wait for the GO.**
2. **Ship** (only after the GO): follow `prompts/02-execute-phase.md`. Pre-flight read-only (re-confirm AS-IS), backup, run steps in order, satisfy each step's criterion **and** positive control, append a phase report from `templates/EXECUTION-LOG.md` after each phase.
3. **Close**: follow `prompts/03-close.md`.

Shortcuts: `/design-and-ship:design <thing>`, `/design-and-ship:ship <run-folder>`, `/design-and-ship:close <run-folder>`.

## Resources in this skill

- `templates/DESIGN.md`, `templates/RUNBOOK.md`, `templates/EXECUTION-LOG.md`
- `prompts/00-claude-md-snippet.md` (always-on rules for a repo's CLAUDE.md or AGENTS.md), `prompts/01-design-phase.md`, `prompts/02-execute-phase.md`, `prompts/03-close.md`
- `examples/cutover-two-workflows.md`: a real run, sanitized, end to end

## Limits (say them honestly)

- It is a **convention**, not a self-enforcing mechanism. It rests on the discipline of the lead and the human.
- For small reversible changes it is excess ceremony: use direct execution.
- If after several runs the Runbook still needs questions outside its gates, the design is ending too early: tighten the falsifiable rule.
