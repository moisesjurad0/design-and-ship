# design-and-ship (plugin)

Claude Code plugin that adds the **design&ship** method: measure first, design long (AS-IS, TO-BE, runbook with rollback per step, decisions), wait for the owner's GO, ship in one go, keep an execution log. It auto-triggers on infrastructure, operations, migration and multi-repo work.

Contents: one skill (`design-and-ship`) with templates, prompts and a worked example, plus three slash commands: `/design-and-ship:design`, `/design-and-ship:ship`, `/design-and-ship:close`. No hooks, no MCP servers, no network access, nothing to configure.

Install:

```text
/plugin marketplace add moisesjurad0/design-and-ship
/plugin install design-and-ship@design-and-ship
```

Full documentation lives in the [repository README](../../README.md).
