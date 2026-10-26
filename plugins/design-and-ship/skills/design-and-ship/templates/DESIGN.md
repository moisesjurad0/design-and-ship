# DESIGN - [short title of the change]

- Status: **DESIGN only**. Nothing was changed in any system. [If you made any write call, declare it here with its effect; read-only validation calls are noted too.]
- Measured on: [YYYY-MM-DD] (read-only queries; scripts in [run folder path]). No secret value was printed.
- Scope: [what is in]. Out of scope: [what is not, and why].
- Human input: [decisions or constraints already given, quoted verbatim with date].
- Labels: **MEASURED** = observed, with its command or source. **INFERRED** = reasoned, not observed.

## 1. AS-IS (measured)

Describe what really exists. One table per area. Every row carries its evidence.

| Resource / piece | Measured state | Source (command, file:line) | Kind |
|---|---|---|---|
| [name] | [state, values, counts] | [command or file] | MEASURED |
| [name] | [what is deduced] | [reasoning] | INFERRED |

### 1.1 Dependencies and consumers

Who uses what. For every resource you intend to touch or delete, list **all** consumers found and how you searched.

| Resource | Consumers found | How it was searched | Possible blind spots |
|---|---|---|---|
| [resource] | [list] | [query] | [what could not be seen] |

### 1.2 Known traps

[Things that were measured and surprised you: stale references, ids that no longer exist, name limits or quotas, etc.]

## 2. TO-BE

Describe the end state in verifiable terms. One sentence per change.

- [End state of piece A]
- [End state of piece B]

### 2.1 Decisions incorporated

| # | Decision | Recommendation | Why | Alternative and its cost |
|---|---|---|---|---|
| T1 | [what is decided] | [recommended option] | [evidence] | [other option: what it costs] |

## 3. Contradictions between items

Resolved **with evidence**. If any is still open, the design is not finished.

| Item A says | Item B says | Evidence that decides | Resolution |
|---|---|---|---|
| [x] | [y] | [measurement] | [which wins and why] |

## 4. Execution plan (summary)

The detail lives in `RUNBOOK.md`. Only order and risk here.

| Phase | What happens | Risk | Rollback (one line) |
|---|---|---|---|
| F1 | [..] | [low/medium/high] | [..] |
| F2 | [..] | [..] | [..] |

## 5. Deletion proposal (requires explicit GO; do not execute)

| Resource | Evidence it is unused | Backup before | Rollback |
|---|---|---|---|
| [resource] | [measurement] | [where it is stored] | [how to recreate, or "none"] |

Pre-deletion checks, to repeat right before: (1) re-confirm nothing references it; (2) save the body of each resource; (3) confirm acceptance tests passed.

**Not proposed for deletion:** [what is kept and why].

## 6. Open decisions (the human answers in writing)

One question at a time in the conversation; all of them are kept here, with a recommendation.

| # | Question | Recommendation | Effect of each answer |
|---|---|---|---|
| D1 | [question] | [recommendation] | [if yes: ..., if no: ...] |

## 7. "Design finished" rule

- [ ] The Runbook can run without asking anything outside its gates.
- [ ] Every step has action, gate, criterion, positive control and rollback.
- [ ] Every contradiction between items is resolved with evidence.

## 8. Risks and what was not measured

| Risk | Likelihood / impact | Mitigation |
|---|---|---|
| [..] | [..] | [..] |

What could **not** be measured, and why: [honest list; "not measurable" is not "ok"].
