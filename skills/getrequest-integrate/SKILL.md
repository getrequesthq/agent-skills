---
name: getrequest-integrate
description: Put getrequest (getrequest.io) in front of an API so every request is queued, retried, and logged. Use when a developer wants to add request logging, automatic retries, webhook buffering, or a mock endpoint without building infrastructure, or asks how to route traffic through a getrequest endpoint URL and choose between Sync API, Async API, and Static API.
---

# Integrate an API with getrequest

getrequest is production ingress for apps and agents (Scalability as a Service for APIs): callers send requests to a getrequest endpoint URL instead of the backend, and getrequest relays, logs, and (optionally) retries them. There is nothing to host and no SDK to install. The integration is a base-URL change.

Docs: https://docs.getrequest.io · Docs MCP server: https://docs.getrequest.io/mcp · getrequest MCP server (manages endpoints): https://app.getrequest.io/mcp

## 1. Create an endpoint

**If the getrequest MCP server is connected** (tools such as `list_projects` and `create_endpoint` are available):

1. Call `list_projects`, show the projects to the user, and **ask which one to use**. Never pick one yourself. Call `create_project` only if the user asks for a new one.
2. Call `create_endpoint` with the project, a name, the method(s), a `mode` (see below), and a `destination` for sync/async.
3. Use the `url` it returns.

If it isn't connected, offer to connect it (https://docs.getrequest.io/guides/mcp; Claude Code: `claude mcp add --transport http getrequest https://app.getrequest.io/mcp`, then approve in the browser). Otherwise, ask the user to create the endpoint in the dashboard:

1. Sign up or sign in at https://app.getrequest.io/register (Free plan: 10,000 requests/month, no card).
2. Open a project → **New Endpoint**.
3. Pick the HTTP method(s) (GET, POST, PUT, PATCH, DELETE; more than one is allowed), an **Action** (see below), and a **Destination URL** for Sync/Async.
4. Copy the generated URL.

Either way, the URL looks like `https://e.getrequest.io/aB3dEf7gH9jK2mN4`. The ID is random, permanent, and the only identifier; there is no project slug in the URL. There is no REST API, SDK, or CLI for managing endpoints, so don't invent one.

## 2. Choose the action

Ask one question: **does the caller need to see what the backend actually returns?**

| Action (UI label, MCP `mode`) | Behaviour | Use for |
|---|---|---|
| Sync API (`sync`) | Proxies inline and returns the destination's exact status, headers, and body. 3s destination timeout, no retries. Backend down → caller gets a real `502`/`504`. | Logins, payments, anything whose response drives the caller's next step |
| Async API (`async`) | Returns `202 {"status":"Success","request_id":"<uuid>"}` once the request is queued, then delivers in the background with 6 attempts over ~36 minutes (immediate, 15s, 60s, 5m, 15m, 15m). 10s timeout per attempt. If it can't be queued, the caller gets `503` (retry it) or `413` (too large to queue) instead. | Webhooks, fire-and-forget events, absorbing traffic spikes |
| Static API (`static`) | Returns a fixed JSON body you configure. No upstream call. | Mocking an API before the backend exists |

The action can be switched later with no downtime (MCP: `update_endpoint` with a new `mode`).

## 3. Point callers at the endpoint

Replace the backend base URL with the endpoint URL. Method, headers, query string, and body are passed through:

```bash
curl -X POST https://e.getrequest.io/<endpoint-id> \
  -H "Content-Type: application/json" \
  -d '{"order_id":"ord_8841","total":59.98}'
```

Headers forwarded to the destination: an allow-list (`Content-Type`, `Authorization`, `Accept`, `Accept-Language`, `Accept-Encoding`, `User-Agent`, `Cache-Control`, `If-None-Match`, `If-Modified-Since`, `X-Request-Id`) plus **any header starting with `X-`**. Other headers are dropped before reaching the destination, but they are still visible in the log.

## 4. Secure both directions (optional)

- **Authentication** checks the caller: Bearer, API Key (header or query), Basic, or HMAC over the raw body. A failure returns `401` and the request is still logged.
- **Destination Authentication** attaches a credential to the outbound call: Bearer, API Key, Basic, or an HMAC signature. Any `Authorization` header the caller sent is replaced.

With the MCP server, pass credentials in `source_auth` / `destination_auth`. Take them from the user or their environment and don't repeat them in the conversation; getrequest never returns them.

Warn the user that configured auth secrets are currently shown unmasked in the dashboard to anyone on the team who can view the project's logs.

## Limits to design around

- Request body: 1 MiB (Sync/Static), 200 KiB for Async text/JSON, and roughly 180 KiB in practice for Async binary bodies.
- Destination URLs cannot resolve to private, loopback, or link-local addresses. These are blocked with `502 {"error":"Destination not allowed"}`.
- Quota exceeded → `429` with `resets_at`. Free plan hard-caps; paid plans bill overage up to 120% of quota.

## Verify

Send a test request, then check the log: `search_logs` / `get_log` with the MCP server, or **Logs** in the dashboard. Every request shows headers, body, response, status, and timing. For Async endpoints the log row appears once the first delivery attempt resolves, which can take up to 10s.

For webhook providers see the `getrequest-webhooks` skill; for failures see `getrequest-troubleshoot`.
