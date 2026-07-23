---
name: Search an Airweave collection
description: Query a synced Airweave collection using the instant, classic, or agentic search tiers and interpret the results.
api: openapi/airweave-openapi.json
operations:
  - instant
  - classic
  - agentic
  - stream-agentic
---

# Search an Airweave collection

Authenticate with the `x-api-key` header. All search calls are scoped to a collection `readable_id`. Base URL `https://api.airweave.ai`.

## Choose a tier

1. **Instant** — `POST /collections/{readable_id}/search/instant` (`instant`). Sub-second vector search; use for autocomplete / low-latency lookups. Body: `InstantSearchRequest` (query, limit, offset, retrieval strategy, filters).
2. **Classic** — `POST /collections/{readable_id}/search/classic` (`classic`). LLM-planned strategy (~2-5s); better recall on ambiguous queries. Body: `ClassicSearchRequest`.
3. **Agentic** — `POST /collections/{readable_id}/search/agentic` (`agentic`). Multi-step navigation across sources for hard questions. Body: `AgenticSearchRequest`.
4. **Agentic (streaming)** — `POST /collections/{readable_id}/search/agentic/stream` (`stream-agentic`). Same as agentic but streams `AgenticSearchEvent`s (thinking, tool-call, reranking, done).

## Rules

- Narrow results with `FilterGroup` / `FilterCondition` metadata filters rather than post-filtering client-side.
- Read `SearchSystemMetadata` and `SearchBreadcrumb` on each `SearchResult` to cite the source of an answer.
- A `404 Collection Not Found` means the `readable_id` is wrong or not yet synced — confirm the setup skill completed.
- Prefer the hosted MCP server (`mcp.airweave.ai/mcp`, tool `search-{collection}`) when operating from an MCP-capable agent (`mcp/airweave-mcp.yml`).
