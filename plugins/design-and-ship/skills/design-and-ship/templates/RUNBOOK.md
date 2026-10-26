# RUNBOOK - [short title of the change]

- Source design: `DESIGN.md` (decisions approved on [YYYY-MM-DD]).
- Execution window: [date and time, time zone]. Reason for the window: [low traffic, etc.].
- Who executes: [lead + agents]. Who approves the gates: [human].
- Log: `EXECUTION-LOG.md` (one report per phase).

## Rules of this execution

- Every call to a cloud or external system names its target **explicitly** (subscription, account, environment). Never rely on the default.
- Backup (no secrets) of everything about to be modified, in the run folder, first.
- Never two active processors on the same input.
- A failed step whose own rollback is not possible: **STOP and report**.
- Whatever is not written here is not done. If something else is needed, go back to design.

## Declared human gates

| Id | Before | What is approved | Who |
|---|---|---|---|
| G1 | [step] | [action with exact scope] | [human] |

## Steps

Mandatory format: **action · gate · criterion · positive control · rollback**.

### Phase 1 - [name] (the current system keeps working)

| # | Action | Gate | Success criterion | Positive control | Rollback |
|---|---|---|---|---|---|
| 1.0 | Back up the current state | none | files saved, no secrets | open one and check it is not empty | n/a |
| 1.1 | [exact action, with the command] | [none / G1] | [observable result] | [how you prove success is not a false positive] | [rollback command] |
| 1.2 | [..] | [..] | [..] | [..] | [..] |

### Phase 2 - [name] (prepare without switching on)

| # | Action | Gate | Success criterion | Positive control | Rollback |
|---|---|---|---|---|---|
| 2.1 | [..] | [..] | [..] | [..] | [..] |

### Phase 3 - [name] (the cutover; only with 1 and 2 green)

| # | Action | Gate | Success criterion | Positive control | Rollback |
|---|---|---|---|---|---|
| 3.1 | [..] | [..] | [..] | [..] | [..] |

## Rollback criterion

When to undo, without asking permission:

- [platform failure: trigger, connection, 5xx, timeout, DNS ...] -> immediate rollback.
- [business error already known in the previous system] -> record and compare; does not trigger rollback by itself.
- [N consecutive failures with no success] -> rollback.

## Rollback by phase

| From | How | Note |
|---|---|---|
| F1 | [..] | [..] |
| F2 | [..] | [..] |
| F3 | [..] | [which filter or guard the previous system carries when switched back on, so it does not repeat what was already processed] |

## After close (explicit leftovers)

- [what stays open, with owner and date if any]
