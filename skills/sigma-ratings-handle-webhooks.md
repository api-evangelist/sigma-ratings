---
name: Handle alerts and webhooks
description: Receive Sigma360 webhook events for monitored entities, verify delivery with the test trigger, and pull the alerts that drove a disposition change.
api: openapi/sigma-ratings-openapi-original.json
operations:
- GET /utils/send-webhook-test
- POST /alerts
- GET /entity/{entityURN}
---

# Handle alerts and webhooks

Use this skill to consume Sigma360's event surface and react to risk changes on monitored entities.

## Authentication
API key in the `Authorization` header. Base URL `https://api.sigma360.com/external/v2`.

## Webhook events (delivered to your registered HTTPS callback URL)
- **Entity Created** — an enqueued entity finished processing.
- **Entity Disposition Changed** — an entity moved between Pending / Cleared / Blocked.
- **History Updated** — the entity's change timeline gained a new item.
- **New Case Management Entry** — a case-management intent was raised.

See `asyncapi/sigma-ratings-webhooks-asyncapi.yml` for payload schemas.

## Steps
1. **Verify delivery** — `GET /utils/send-webhook-test` to trigger a test callback to your endpoint and confirm connectivity/signature handling before going live.
2. **On a `Entity Disposition Changed` event** — read the driving alerts with `POST /alerts` (filtered to the entity) to understand which indicators fired.
3. **Refresh the entity** — `GET /entity/{entityURN}` to fetch the current disposition and risk profile after the event.

## Conventions & error handling
- Payloads are JSON delivered over HTTPS POST; respond `2xx` promptly and process asynchronously.
- Alerts list responses page with `cursor`/`page`/`pageSize`.
- Errors are plain-text with HTTP status; handle `429` with backoff. See `errors/sigma-ratings-problem-types.yml`.
