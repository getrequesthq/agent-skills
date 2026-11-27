# AGENTS.md

Instructions for AI coding agents (Claude Code, Cursor, Codex, Copilot, etc.) working in this repository or helping a developer integrate getrequest.

## What this repo is

Official agent skills for [getrequest](https://www.getrequest.io), Scalability as a Service for APIs: production ingress for apps and agents. Callers send requests to a getrequest endpoint URL (`https://e.getrequest.io/<endpoint-id>`), and getrequest relays them to the destination (Sync API), acknowledges and delivers them in the background with retries (Async API), or returns a fixed body (Static API). Every request is logged.

- `skills/<name>/SKILL.md`: one skill per directory, with YAML frontmatter (`name`, `description`) followed by markdown instructions.
- `plugin.json`: Agent Plugins manifest (https://agent-plugins.org/specification).
- `mcp.json`: two MCP servers. `getrequest` (`https://app.getrequest.io/mcp`) manages the user's projects, endpoints and request logs, with OAuth sign-in in the browser. `getrequest-docs` (`https://docs.getrequest.io/mcp`) searches the docs, no sign-in.

## Rules for editing skills

- Facts must match the docs at https://docs.getrequest.io (limits, status codes, retry schedule, plan gating). If the docs change, update the skills in the same change.
- Endpoints are managed through the getrequest MCP server (tools: `whoami`, `list_projects`, `create_project`, `list_endpoints`, `get_endpoint`, `create_endpoint`, `update_endpoint`, `search_logs`, `get_log`, `list_failed_deliveries`, `list_bulk_retry_jobs`, `get_usage`) or in the dashboard at https://app.getrequest.io. There is no REST API, SDK, or CLI. Do not invent one.
- Skills must tell agents to ask the user which project to use, never pick one. Agents can't replay, bulk retry, or delete through the MCP server; those stay in the dashboard.
- Keep `name` in frontmatter identical to the directory name, lowercase with hyphens.
- `description` says what the skill does **and** when to use it, in one or two sentences.
- These skills are mirrored at https://www.getrequest.io/.well-known/agent-skills/. Update the website copy (`content/skills/` in the website repo) when a skill changes.

## Helping a developer integrate getrequest

1. Use the `getrequest-integrate` skill to pick Sync, Async, or Static, create the endpoint (with the getrequest MCP server if it's connected), and swap the base URL.
2. For webhook providers, use `getrequest-webhooks`. `Stripe-Signature` is not forwarded, so verify it in getrequest with HMAC Authentication.
3. For error responses or failed deliveries, use `getrequest-troubleshoot`.
4. For anything else, query the docs MCP server or https://docs.getrequest.io/llms.txt.

## Links

- Website: https://www.getrequest.io (markdown: https://www.getrequest.io/index.md)
- llms.txt: https://www.getrequest.io/llms.txt
- Docs: https://docs.getrequest.io
- Pricing: https://www.getrequest.io/pricing.md
- Support: support@getrequest.io
