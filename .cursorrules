# AGENTS.md

Instructions for AI coding agents (Claude Code, Cursor, Codex, Copilot, etc.) working in this repository or helping a developer integrate getrequest.

## What this repo is

Official agent skills for [getrequest](https://www.getrequest.io), Scalability as a Service for APIs. Callers send requests to a getrequest endpoint URL (`https://e.getrequest.io/<endpoint-id>`), and getrequest relays them to the destination (Sync API), acknowledges and delivers them in the background with retries (Async API), or returns a fixed body (Static API). Every request is logged.

- `skills/<name>/SKILL.md`: one skill per directory, with YAML frontmatter (`name`, `description`) followed by markdown instructions.
- `plugin.json`: Agent Plugins manifest (https://agent-plugins.org/specification).
- `mcp.json`: the getrequest docs MCP server (`https://docs.getrequest.io/mcp`).

## Rules for editing skills

- Facts must match the docs at https://docs.getrequest.io (limits, status codes, retry schedule, plan gating). If the docs change, update the skills in the same change.
- There is no public management API yet. Endpoints are created in the dashboard at https://app.getrequest.io. Do not invent REST endpoints, SDK calls, or CLI commands.
- Keep `name` in frontmatter identical to the directory name, lowercase with hyphens.
- `description` says what the skill does **and** when to use it, in one or two sentences.
- These skills are mirrored at https://www.getrequest.io/.well-known/agent-skills/. Update the website copy (`content/skills/` in the website repo) when a skill changes.

## Helping a developer integrate getrequest

1. Use the `getrequest-integrate` skill to pick Sync, Async, or Static and swap the base URL.
2. For webhook providers, use `getrequest-webhooks`. `Stripe-Signature` is not forwarded, so verify it in getrequest with HMAC Authentication.
3. For error responses or failed deliveries, use `getrequest-troubleshoot`.
4. For anything else, query the docs MCP server or https://docs.getrequest.io/llms.txt.

## Links

- Website: https://www.getrequest.io (markdown: https://www.getrequest.io/index.md)
- llms.txt: https://www.getrequest.io/llms.txt
- Docs: https://docs.getrequest.io
- Pricing: https://www.getrequest.io/pricing.md
- Support: support@getrequest.io
