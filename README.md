# getrequest agent skills

Official [Agent Skills](https://skills.sh) for [getrequest](https://www.getrequest.io): Production Ingress for Apps & Agents. Code in hours. Scale in minutes.

These skills teach AI agents how to put an API behind getrequest, capture and verify webhooks, and diagnose failed requests.

| Skill | Use it when |
|---|---|
| [`getrequest-integrate`](skills/getrequest-integrate/SKILL.md) | Adding queuing, retries, and request logging in front of an API, or mocking an API |
| [`getrequest-webhooks`](skills/getrequest-webhooks/SKILL.md) | Receiving Stripe/GitHub/Shopify webhooks reliably, with signature verification |
| [`getrequest-troubleshoot`](skills/getrequest-troubleshoot/SKILL.md) | Decoding 401/404/405/413/429/502/504 responses and recovering failed requests |

## Install

```bash
npx skills add getrequesthq/agent-skills
```

Or install as an [Agent Plugin](https://agent-plugins.org) using `plugin.json` in this repo. The plugin also wires up both MCP servers below.

## Connect the getrequest MCP server

The skills work best with the getrequest MCP server connected, so your agent can create endpoints and read the request log itself:

```bash
# Claude Code
claude mcp add --transport http getrequest https://app.getrequest.io/mcp

# Codex
codex mcp add getrequest --url https://app.getrequest.io/mcp && codex mcp login getrequest
```

Any other MCP client: add `https://app.getrequest.io/mcp` as a remote (HTTP) server. The first time it's used, your browser opens to sign in and approve the connection; there's no API key. Setup for Cursor, VS Code and stdio-only clients: https://docs.getrequest.io/guides/mcp

| Server | URL | Sign-in |
|---|---|---|
| `getrequest` | `https://app.getrequest.io/mcp` | OAuth in the browser. Manages projects and endpoints, reads logs and usage |
| `getrequest-docs` | `https://docs.getrequest.io/mcp` | None. Searches the docs |

## Links

- Website: https://www.getrequest.io
- Docs: https://docs.getrequest.io
- MCP setup: https://docs.getrequest.io/guides/mcp
- llms.txt: https://www.getrequest.io/llms.txt
- Skills index: https://www.getrequest.io/.well-known/agent-skills/index.json
- Support: support@getrequest.io

## License

MIT
