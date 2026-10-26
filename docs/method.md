# The design&ship method

> You spend the time on design. Execution is clean, in one go, and as short as possible.

design&ship is the sum of two ideas:

- **plan&code**: how work is organized. One plan per item, then a plan that unifies them, then parallelize what does not collide.
- **doc-set**: which documents remain. Design Doc, Plan/Runbook and, if needed, ADR and Playbook. Nothing else.

## 1. When to use it

| Situation | Use |
|---|---|
| Trivial fix: you can write the correct result in one line and the error is reversible | Direct execution |
| Infra, operations, migrations, cutovers, deletions, or work crossing several repos or systems | **design&ship** |
| New code with substantial requirements ambiguity (what to build) | SDD / spec-driven development: its `design.md` and `tasks.md` act as Design Doc and Plan |

Quick decision rule for each item, before touching anything:

> Can I write **now**, in one line, the correct result **and why** it is correct, **and** is the error reversible?
> - Yes to both: direct execution.
> - No to either: dedicated plan that **measures first**.

design&ship is for the second case. It usually appears when something is irreversible, expensive, production-facing, or when several items touch each other.

## 2. Phases

```
0 Anchor -> 1 Measure and plan per item -> 2 Unify (Design Doc + Runbook)
         -> human GO on decisions -> 3 Execute in one go -> 4 Close
```

| Phase | Who | Produces |
|---|---|---|
| **0 Anchor** | Lead + human | Work recorded somewhere durable (ticket, note, issue) before starting. No working on the fly |
| **1 Measure and plan per item** | Agents (read-only, in parallel) | One *input* per item: what really exists (AS-IS **measured**), what is proposed, what can go wrong. Kept in `inputs/`; not final documents |
| **2 Unify** | Lead | **Design Doc** (AS-IS, TO-BE, decisions, deletion proposal, open questions) and **Runbook** (ordered steps with gate, criterion, positive control and rollback). Cross-item contradictions are caught here |
| **GO** | Human | Answers the consolidated question list and approves the Runbook. Without it, nothing executes |
| **3 Execute** | Lead + agents | The Runbook runs in one batch, with a **report per phase** in the EXECUTION-LOG. Stops only at human gates or on failure |
| **4 Close** | Lead | Final documents updated, log closed, explicit leftovers, commit and push |

### The falsifiable rule: "is the design finished?"

It is finished **if and only if** all three hold:

1. The Runbook can run **without asking the human anything**, except at its declared gates.
2. **Every step** has: **action · gate · criterion · positive control · rollback**.
3. **Every contradiction between items** is resolved **with evidence**, not by eye.

If one fails, do not execute: go back to phase 2.

### Parallelizing (phases 1 and 3)

- Two jobs are parallelizable **iff** the intersection of what each **writes** is empty. If they cross, serialize.
- Work that **writes files** goes in its own isolated copy (for example a `git worktree`) and is integrated at the end. Work that only reads or measures can run in the main tree.
- Each plan declares four things so it can be unified: what it **touches**, what it **depends on**, what it **blocks**, and its **honest verdict** (worth it / not worth it / accepted debt).

## 3. Roles

| Role | Does | Does not |
|---|---|---|
| **Human** | Decides: approves the design, answers the questions, gives the GO, approves production gates and deletions | Is not the one who measures or types the steps |
| **Lead** (main agent) | Orchestrates: assigns measurements, unifies, writes the documents, runs the Runbook, reports | Does not decide for the human or invent what it did not measure |
| **Agents** (subagents) | Measure and execute bounded steps; return evidence | Do not widen scope or ask questions outside the gates |

## 4. Safety rules (what makes it work)

These are not decorative. Each one was born from a real failure.

1. **Measure before asserting.** "Doesn't exist", "is broken", "is zero", "nobody uses it" require a direct measurement, with the command in view. A truncated output or an empty listing does not prove absence: confirm the query reached what you think it reached.
2. **Label every claim MEASURED or INFERRED.** MEASURED carries its source (command, file and line). INFERRED is reasoning without observation. **An inference is never promoted to fact**, neither when summarizing nor when reporting.
3. **Ask the real system, not a proxy.** Counting mentions in a text does not count writers; reading a router's structure does not prove the contract. If a test "lies", mutate the code and see whether it goes red.
4. **Proposal before deleting.** Every deletion enters first as a proposal in the Design Doc, with evidence it is unused. It runs only with the human's explicit GO.
5. **Backup before anything destructive.** Save the prior state (no secrets) in the run folder before deleting, moving or overwriting.
6. **Rollback per step.** If a step has no written rollback, it is not ready. If it truly has none, it is a human gate.
7. **One question at a time.** When something blocks, ask a single question, with your recommendation, and wait. Before asking, re-read the original request: it is often already answered.
8. **One writer per resource.** Never two agents writing the same thing. Never two systems processing the same input at once (for example, two triggers on the same mailbox).
9. **Cut with a time filter if replay is possible.** When swapping one event processor for another, define a cutoff instant and discard everything earlier: a new trigger can reprocess a backlog and duplicate effects.
10. **Do not notify third parties unless asked.** Do not notify other approvers or outside channels; the human decides who is told what.
11. **Ask for the exact authorization, not the command.** The human approves an action with its scope (what, where, when). Approval does not extend to other plans or batches.
12. **No secrets in documents.** Secret names yes, values never. Do not print or copy state files that contain them.
13. **Fail red, not green.** A check that could not run is not a passed check. A "no data" result is reported as "not measurable", not "ok".
14. **If there is contradiction or ambiguity, stop and report it.** Do not choose.
15. **A suite that turns greener is a warning.** If a change makes tests pass that used to fail, verify it has not stopped measuring.

## 5. Getting started in five minutes

1. Install the plugin (see the [README](../README.md)), or paste [`00-claude-md-snippet.md`](../plugins/design-and-ship/skills/design-and-ship/prompts/00-claude-md-snippet.md) into your project's `CLAUDE.md`.
2. Create a folder for the run, e.g. `plans/2026-01-15-my-change/`, with `inputs/` inside.
3. Run `/design-and-ship:design <your change>`, or paste [`01-design-phase.md`](../plugins/design-and-ship/skills/design-and-ship/prompts/01-design-phase.md) filled in. Review the `DESIGN.md` and `RUNBOOK.md` it produces.
4. Answer the open decisions in writing. Give the GO.
5. Run `/design-and-ship:ship <run folder>` with your decisions. Watch the phase reports.
6. Run `/design-and-ship:close <run folder>`.

See [the worked example](examples/cutover-two-workflows.md) for the full path on a real case.

## 6. Limits

- It is a **convention**, not a self-enforcing mechanism. It rests on the discipline of the lead and the human.
- For small reversible changes the method is excess ceremony: use direct execution.
- If after several runs the Runbook still needs questions outside its gates, the design is ending too early: tighten the falsifiable rule.
