---
name: Screen an entity against Sigma360 risk data
description: Run a one-off screening of a person or organization against Sigma360's sanctions, watchlist, adverse-media and risk data, and read the results.
api: openapi/sigma-ratings-openapi-original.json
operations:
- GET /account/status
- GET /info/sources
- POST /screening/full-single
---

# Screen an entity against Sigma360

Use this skill to perform a one-off (non-monitored) screening of an entity against Sigma360's risk data.

## Authentication
All requests require an API key in the `Authorization` header (see `authentication/sigma-ratings-authentication.yml`). Base URL is `https://api.sigma360.com/external/v2`.

## Steps
1. **Confirm account access** — `GET /account/status` to verify your organization-wide metadata and that the key is valid.
2. **(Optional) List available sources** — `GET /info/sources` to see which watchlists / registries / data sources your filter set can draw on.
3. **Screen the entity** — `POST /screening/full-single` with the entity name and filter-set selection. The response returns risk indicators, KYC details, and adverse-media matches determined by your pre-defined Filter Set.

## Conventions & error handling
- Results are shaped by your **Filter Set** — the same name can return different indicators under different filter sets.
- List responses page with `cursor` / `page` / `pageSize` and sort with `sortBy` / `sortOrder`.
- Errors return a plain-text body with the HTTP status (not RFC 9457). Handle `401` (bad/missing key), `429` (rate limited — back off), and `500` (retry with backoff). See `errors/sigma-ratings-problem-types.yml`.
