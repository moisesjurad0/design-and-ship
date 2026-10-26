# Security Policy

## Scope

This repository contains Markdown documents, prompts, templates and JSON manifests for a Claude Code plugin. It has no hooks, no MCP servers, no executable code and makes no network calls. Security reports are still welcome, for example:

- A prompt or template that could lead an agent to leak secrets, widen its scope or skip a human gate.
- A manifest change that would add executable behavior (hooks, MCP servers) without being declared.
- Sanitized examples that still contain identifying or sensitive information.

## Reporting a vulnerability

Please **do not open a public issue** for sensitive reports. Use GitHub's private vulnerability reporting: the **Security** tab of this repository, then **Report a vulnerability**.

You can expect an acknowledgement within 7 days. Because the project is documentation, most fixes are a text change released in the next patch version.

## Supported versions

Only the latest released version receives fixes.
