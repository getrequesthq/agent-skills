# getrequest agent skills

Official [Agent Skills](https://skills.sh) for [getrequest](https://www.getrequest.io): Scalability as a Service for your API. Setup in minutes. Scale to millions.

These skills teach AI agents how to put an API behind getrequest, capture and verify webhooks, and diagnose or replay failed requests.

| Skill | Use it when |
|---|---|
| [`getrequest-integrate`](skills/getrequest-integrate/SKILL.md) | Adding queuing, retries, and request logging in front of an API, or mocking an API |
| [`getrequest-webhooks`](skills/getrequest-webhooks/SKILL.md) | Receiving Stripe/GitHub/Shopify webhooks reliably, with signature verification |
| [`getrequest-troubleshoot`](skills/getrequest-troubleshoot/SKILL.md) | Decoding 401/404/405/413/429/502/504 responses and recovering failed requests |

## Install

```bash
npx skills add getrequesthq/agent-skills
```

Or install as an [Agent Plugin](https://agent-plugins.org) using `plugin.json` in this repo. The plugin also wires up the getrequest docs MCP server (`https://docs.getrequest.io/mcp`).

## Links

- Website: https://www.getrequest.io
- Docs: https://docs.getrequest.io
- llms.txt: https://www.getrequest.io/llms.txt
- Skills index: https://www.getrequest.io/.well-known/agent-skills/index.json
- Support: support@getrequest.io

## License

MIT
