# Contributing

Thanks for helping. The most valuable contribution is a **real run**: an sanitized example or a lesson learned from using the method.

## Ways to contribute

- **Worked examples** in `docs/examples/` (database migration, DNS change, secret rotation, multi-repo refactor...). Use the structure of the existing example: problem, measured findings, design decisions, runbook, execution, what it teaches.
- **Lessons** that change a rule, a template or a prompt. Say which real failure motivated it; every rule here came from one.
- **Bug reports** for the plugin (does not trigger, wrong behavior, broken manifest).
- **Translations** of the README.

## Ground rules

1. **Sanitize everything.** No company names, internal hostnames, subscription or tenant ids, emails, chat ids, token or secret names, or names of colleagues. Counts and timings may stay.
2. **Keep the method falsifiable.** A new rule should say how you would know it was violated.
3. **MEASURED or INFERRED.** The docs practice what they preach: label claims in examples.
4. **No executable code in the plugin** without discussing it first in an issue. It is Markdown-only by design: no hooks, no MCP servers, no network.
5. English is the primary language of the plugin content.

## Development

```bash
# validate the marketplace and the plugin (Claude Code CLI)
claude plugin validate .
claude plugin validate plugins/design-and-ship

# try it locally
claude plugin marketplace add ./
claude plugin install design-and-ship@design-and-ship
```

CI (`.github/workflows/validate.yml`) checks that the manifests are valid JSON with the required fields and that Markdown links resolve.

## Pull requests

- Use [conventional commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`, `docs:`, `chore:`).
- One focused change per PR. Update `CHANGELOG.md` under `[Unreleased]`.
- Fill in the PR template.

By contributing you agree your contribution is licensed under the [MIT License](LICENSE) and that you follow the [Code of Conduct](CODE_OF_CONDUCT.md).
