---
name: textql-run-an-analysis
description: Ask Ana a question against a TextQL connector and retrieve the answer, the generated SQL/Python and any charts or reports it produced.
api: TextQL v2 API
base_url: https://app.textql.com/v2
operations:
  - v2.listConnectors
  - v2.createChat
  - v2.streamChat
  - v2.getChat
  - v2.getChatCells
  - v2.cancelStream
generated: '2026-08-30'
method: generated
source: openapi/textql-v2-openapi.yml + https://docs.textql.com/api-reference/v2/introduction
---

# Run an analysis with Ana

Ask a natural-language question against connected data and get back a grounded answer plus the exact
SQL and Python that produced it.

## Before you start

- Authenticate with `Authorization: Bearer <key>`. See `authentication/textql-authentication.yml`.
- If you are calling over MCP instead of REST, the equivalent tools are `ana` (synchronous) and
  `ana_ask` / `ana_poll` (streaming). See `mcp/textql-mcp.yml`.

## Steps

1. **Find a connector to scope the question to.**
   Call `v2.listConnectors` (`GET /v2/connectors`). Connector ids are plain integers. Scoping is
   optional but strongly preferred — an unscoped question makes Ana choose the source for you.

2. **Choose synchronous or streaming.** This is the decision that matters most.
   - Short question, tolerant caller: `v2.createChat` (`POST /v2/chats`) with
     `{"question": "...", "connector_ids": [1]}`. Documented as taking **up to a minute**.
   - Anything longer, or any caller with a request timeout: `v2.streamChat`
     (`POST /v2/chats/stream`), which returns `text/event-stream`.

   If the synchronous call returns `504 timeout`, that is the documented signal to switch to the
   streaming surface — not to retry the same call.

3. **Do not blind-retry.** There is no idempotency key on this API
   (`conventions/textql-conventions.yml`). Retrying a timed-out `POST /v2/chats` starts a *second*
   billable analysis run and consumes ACUs again. On a timeout, read back with `v2.getChat` before
   deciding anything was lost.

4. **Read the answer and the work.**
   - `v2.getChat` (`GET /v2/chats/{id}`) returns the chat with messages and assets.
   - `v2.getChatCells` (`GET /v2/chats/{id}/cells`) returns per-step execution detail — the generated
     SQL and Python, the outputs, and the assets. This is the audit trail; use it whenever the answer
     will be acted on.
   - `v2.getChatCell` fetches one cell by id.

5. **Cancel if you need to stop.**
   `v2.cancelStream` (`POST /v2/chats/{id}/cancel`) stops a running stream. It stops further work; it
   does not undo work already done or refund ACUs already consumed.

## Errors

Branch on `error.code`, not on the message string. The enum is closed — see
`errors/textql-problem-types.yml`.

- `timeout` (504) — switch to `v2.streamChat`.
- `rate_limit_exceeded` (429) — back off. **No `Retry-After` or `RateLimit-*` header is returned**,
  so use your own backoff schedule; you get no hint of how long to wait.
- `permission_denied` (403) — check the key's *owner* as well as its roles. Assumed roles are a
  snapshot taken at key creation and never widen.
- `execution_failed` — the platform worked and the generated SQL or Python did not. Read the cell
  output; do not retry unchanged.

## Limits

Row and time ceilings are published per organization at
https://docs.textql.com/core/admin/limits — max SQL rows 2,000,000, max run SQL time 180,000 ms,
stream timeout 200,000 ms. See `rate-limits/textql-rate-limits.yml`.
