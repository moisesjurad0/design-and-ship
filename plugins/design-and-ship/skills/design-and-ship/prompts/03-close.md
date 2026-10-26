# Prompt 03 - Close

````text
design&ship: CLOSE of [run folder].

Do this, and nothing more:

1. Re-read DESIGN.md, RUNBOOK.md and EXECUTION-LOG.md. Update DESIGN.md: mark which decisions were taken and how, and which part of the TO-BE is really standing (MEASURED with command, or INFERRED). Where execution deviated from the design, fix the text or add a note; leave no document describing something that is no longer true.
2. Turn whatever is operated recurrently into an operational RUNBOOK (routine, known failures, how to revert). If a repeatable class of failure appeared, create a short PLAYBOOK (decision tree). If there was an architecture decision, create a short ADR (context, decision, consequences); an ADR is not edited later, it is replaced by another.
3. Check that the documents contain no secrets, tokens or sensitive values. Secret names only.
4. List the open leftovers, each with owner and date if there is one. What could not be measured is written as "not measurable", not "ok".
5. Close the anchor record (ticket, note or issue) with a 5-line summary and a link to the run folder.
6. A "Lessons" section in EXECUTION-LOG.md: what to adjust in the method or the templates.
7. Commit ONLY the files you touched (explicit pathspec, never "add all"), conventional message, and push.

Hand me: list of files, what remains pending, and confirmation that the secrets sweep found nothing.
````
