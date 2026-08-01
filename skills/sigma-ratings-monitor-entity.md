---
name: Create and monitor an entity
description: Enroll an entity for continuous ("perpetual KYC") monitoring in Sigma360, track the async creation task, then retrieve the entity and its history.
api: openapi/sigma-ratings-openapi-original.json
operations:
- POST /entities/create/enqueue
- GET /entities/create/status/{taskID}
- GET /entity/{entityURN}
- POST /entity/{entityURN}/history
- PUT /entities/archive
---

# Create and monitor an entity

Use this skill to add a "My Entity" to Sigma360 for ongoing monitoring and to work with it afterward.

## Authentication
API key in the `Authorization` header. Base URL `https://api.sigma360.com/external/v2`.

## Steps
1. **Enqueue creation (async)** — `POST /entities/create/enqueue` with the entity details. This returns a `taskID`; entity creation is processed asynchronously. (For synchronous creation use `POST /entities/create`; to skip screening use `POST /entities/create/assume-clean`.)
2. **Poll the task** — `GET /entities/create/status/{taskID}` (or `GET /entities/by-task-id/current-state/{taskID}`) until the task completes and yields the entity's `entityURN`. A `425 Too Early` means the task is still processing — wait and retry.
3. **Retrieve the entity** — `GET /entity/{entityURN}` for the full entity profile and current risk disposition.
4. **Read history** — `POST /entity/{entityURN}/history` to retrieve the change timeline for the entity.
5. **Archive when done** — `PUT /entities/archive` to stop monitoring entities you no longer need.

## Conventions & error handling
- Creation is **asynchronous** — never assume the entity exists until the task reports completion.
- No idempotency-key header is available; guard against duplicate enqueues on your side.
- Register a webhook to be notified of `Entity Created` and `Entity Disposition Changed` events instead of polling (see `skills/sigma-ratings-handle-webhooks.md`).
- Errors are plain-text with HTTP status; see `errors/sigma-ratings-problem-types.yml`.
