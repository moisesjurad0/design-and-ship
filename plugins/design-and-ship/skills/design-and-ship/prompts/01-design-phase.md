# Prompt 01 - Design phase (measure, write, STOP)

Fill in the bracketed parts and paste the block into your agent. Built for Claude Code or any agent with terminal access.

````text
design&ship: DESIGN PHASE for [name of the change].

GOAL OF THE CHANGE
[What you want to achieve, in 3 to 6 lines. Desired end state.]

CONTEXT AND CONSTRAINTS (my words, respect them)
- [constraint 1, e.g. "no interactive authorization step is allowed"]
- [constraint 2, e.g. "the cutover window is Saturday night"]
- [what must NOT be touched]

RUN FOLDER
[path, e.g. plans/YYYY-MM-DD-name/]  (create inputs/ inside)

RULES OF THIS PHASE (hard)
1. READ-ONLY. Change nothing in any system, remote repository or pipeline. If a validation requires a POST, it must be read-only and you declare it in the DESIGN header.
2. Every command against the cloud or an external system explicitly names its target (account, subscription, environment). Never use the default.
3. Label EVERY claim MEASURED (with the command or file and line) or INFERRED. Never turn an inference into a fact.
4. Before claiming "does not exist", "is broken", "is zero" or "nobody uses it", measure directly and exactly. An empty or truncated listing proves nothing: check that the query reached what you think it did.
5. Do not print secret values (secret names only). Do not open state files that contain them.
6. Deletions are NOT executed: they go as a proposal, with evidence they are unused and the prior backup.
7. If there is a contradiction or ambiguity between what I ask and what you measure, do not choose: report it.
8. One question at a time, with your recommendation. Before asking, re-read this message: it may already be answered.

PROCEDURE
A. Break the work into items. For each item apply: "can I write now, in one line, the correct result and why, and is the error reversible?". If yes to both, note it as direct execution. If not, write an input.
B. For each doubtful item, launch a read-only measurement (in parallel if they do not collide) and save the result in inputs/<item>.md. Each input declares: what it touches, what it depends on, what it blocks, and its honest verdict (worth it / not worth it / accepted debt).
C. Unify: read the inputs, cross their "touches" sets, detect contradictions and resolve them with evidence (e.g. name limits, regions or quotas that collide between items). Two items are parallelizable only if what they write does not intersect.
D. Write, using the templates in templates/:
   - DESIGN.md: measured AS-IS, TO-BE, decisions with recommendation, resolved contradictions, deletion proposal, open decisions.
   - RUNBOOK.md: steps with action, gate, criterion, positive control and rollback; declared human gates; rollback criterion.
E. Check the "design finished" rule: (1) the Runbook runs without asking me anything outside its gates, (2) every step carries its five fields, (3) no contradiction is left unresolved. If anything fails, fix it before handing over.

DELIVERY AND STOP
- Summarize in at most 15 lines: verdict, findings that change the plan, and the consolidated list of open decisions (each with your recommendation).
- Commit only the files of the run folder, with an explicit pathspec.
- STOP. Execute nothing. Wait for my answers to the decisions and my GO.
````
