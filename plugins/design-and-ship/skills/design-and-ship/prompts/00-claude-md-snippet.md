# Snippet for your project's CLAUDE.md (or AGENTS.md)

Paste this as-is. It activates the `design&ship` keyword and fixes the rules the agent must respect. (If you installed the plugin, the skill already carries this; use the snippet when you want the rules always-on in a repo, or with agents other than Claude Code.)

```markdown
## Implementation method: design&ship

Keyword: when the user writes `design&ship <thing>`, follow the full method.

Before implementing anything non-trivial, name the method that applies:
- Trivial, reversible fix (one line + its test): direct.
- Infra, operations, migrations, cutovers, deletions or multi-repo work: **design&ship**.
- Code with substantial requirements ambiguity: a specification method (SDD or similar).

design&ship:
0. Anchor the work in a durable record. Do not work on the fly.
1. Measure (read-only) and write one input per item, in `inputs/`.
2. Unify into `DESIGN.md` (measured AS-IS, TO-BE, decisions, deletion proposal) and `RUNBOOK.md`.
3. STOP. Ask the human for the GO on the decisions. Do not execute without it.
4. Execute the Runbook in one go, with one report per phase in `EXECUTION-LOG.md`.
5. Close: final documents, explicit leftovers, commit and push.

The design is finished only if: (a) the Runbook runs without asking anything outside its
gates, (b) every step has action, gate, criterion, positive control and rollback,
(c) every contradiction between items is resolved with evidence.

Hard rules:
- Label every claim MEASURED (with its source) or INFERRED. Never promote an inference to fact.
- Measure before claiming "does not exist / is broken / is zero". An empty or truncated listing is not proof.
- Deletions go first as a proposal; they run only with an explicit GO.
- Backup (no secrets) before any destructive step. Written rollback for every step.
- One question at a time, with your recommendation; then wait.
- Never print secret values or open state files that contain them.
- Do not notify third parties unless the human asks.
- If there is a contradiction or ambiguity, stop and report it; do not choose.
```
