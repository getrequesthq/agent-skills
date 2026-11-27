---
name: getrequest-webhooks
description: Receive third-party webhooks (Stripe, GitHub, Shopify, etc.) through getrequest so they are acknowledged instantly, verified, logged, and retried until the backend accepts them. Use when a developer is losing webhooks during deploys or outages, needs to inspect webhook payloads, or must verify webhook signatures such as Stripe-Signature or X-Hub-Signature-256 through getrequest.
---

# Capture and deliver webhooks with getrequest

Webhook providers want a fast `2xx` and retry on their own schedule if they do not get one. A getrequest **Async API** endpoint answers the provider immediately and takes responsibility for delivering to your backend.

## Setup

1. Create an endpoint with method `POST`, action **Async API**, and your webhook handler as the **Destination URL**. With the getrequest MCP server, ask the user which project to use, then call `create_endpoint` with `mode: "async"` (and the HMAC settings below in `source_auth`). Otherwise create it in https://app.getrequest.io.
2. Register the endpoint URL (`https://e.getrequest.io/<endpoint-id>`) as the webhook URL in the provider's dashboard.
3. Configure signature verification (below).

The provider receives `202 {"status":"Success","request_id":"<uuid>"}` straight away, once the request is queued. If getrequest can't queue it, the provider gets a `503` instead and retries on its own schedule, so the event isn't lost. getrequest then delivers to your backend with up to 6 attempts over about 36 minutes (immediate, 15s, 60s, 5m, 15m, 15m). Any `2xx` marks the delivery as `delivered`. After the last attempt it is marked `failed` and can be replayed manually on paid plans (Pro, Gro).

Each attempt re-reads the endpoint's current destination and credentials, so fixing the handler or rotating a secret mid-retry takes effect on the next attempt.

## Signature verification: choose where it happens

getrequest forwards only allow-listed headers and headers starting with `X-`:

| Provider header | Reaches your backend? | Where to verify |
|---|---|---|
| `X-Hub-Signature-256` (GitHub) | Yes | Your handler, or getrequest |
| `X-Shopify-Hmac-Sha256` | Yes | Your handler, or getrequest |
| `Stripe-Signature` | **No**, it is stripped | **getrequest only** |

To verify in getrequest, set the endpoint's **Authentication** to `HMAC` (MCP: `source_auth` with `type: "hmac"` and the `hmac_*` fields) and fill in:

- **Header Key**: the provider's signature header
- **Signing Secret**: the provider's webhook secret
- **Advanced**: algorithm (`SHA-256`/`SHA-1`), encoding (`Base64`/`Hex`), and prefix (e.g. `sha256=`)

The HMAC is computed over the raw request body with a constant-time compare. Invalid or missing signatures get `401` and never reach the backend, but they are still logged so probing is auditable.

Without Authentication, anyone holding the endpoint URL can push arbitrary payloads to the backend. Always configure one of the two verification paths.

## Proving the request came from getrequest

Set **Destination Authentication** (Bearer, API Key, Basic, or an HMAC signature on the outbound body) so the backend can reject traffic that did not pass through getrequest.

## Limits

- Async bodies: 200 KiB. Binary payloads travel base64-encoded, so keep them under about 180 KiB. A request too large to queue gets `413` ("Request is too large to queue for async delivery") instead of a `202`, and is logged.
- Destination timeout: 10s per attempt.
- A destination that resolves to a private or internal address is blocked and not retried.

## Inspecting deliveries

Open **Logs** and click a request (MCP: `search_logs`, then `get_log`) to see the original headers and body exactly as the provider sent them, plus every delivery attempt (status, duration, error). Credential headers are redacted in MCP output. `delivery_status` is one of `retrying`, `delivered`, or `failed`.

Docs: https://docs.getrequest.io/guides/capture-webhooks · https://docs.getrequest.io/guides/async-api
