---
name: Set up a collection and connect a source
description: Create an Airweave collection, connect a data source to it, and trigger the first sync so the collection becomes searchable.
api: openapi/airweave-openapi.json
operations:
  - create-collections-post
  - create-source-connections-post
  - run-source-connections-source-connection-id-run-post
  - get-source-connection-jobs-source-connections-source-connection-id-jobs-get
---

# Set up a collection and connect a source

Authenticate every request with the `x-api-key` header (see `authentication/airweave-authentication.yml`). Base URL is `https://api.airweave.ai`.

## Steps

1. **Create a collection** — `POST /collections` (`create-collections-post`). Provide a name; Airweave returns a `readable_id` you use for all later calls.
2. **Connect a source** — `POST /source-connections` (`create-source-connections-post`). Reference the collection's `readable_id` and the source `short_name` (list options via `GET /sources`, `list-sources-get`). Supply credentials via one of the authentication shapes: `DirectAuthentication`, `OAuthTokenAuthentication`, `OAuthBrowserAuthentication`, or `AuthProviderAuthentication`.
3. **Trigger the first sync** — `POST /source-connections/{source_connection_id}/run` (`run-source-connections-source-connection-id-run-post`). A `409 Sync Already Running` means one is in progress — wait or cancel before re-running.
4. **Poll job status** — `GET /source-connections/{source_connection_id}/jobs` (`get-source-connection-jobs-...`) until the latest job reaches a terminal `SyncJobStatus`.

## Rules

- Respect rate limits: back off on `429` using `Retry-After` and the `RateLimit-*` headers (`rate-limits/airweave-rate-limits.yml`).
- Errors are custom JSON envelopes, not RFC 9457 (`errors/airweave-problem-types.yml`).
- Subscribe to lifecycle events (`sync.completed`, `source_connection.auth_completed`) via webhooks instead of tight polling (`asyncapi/airweave-webhooks.yml`).
