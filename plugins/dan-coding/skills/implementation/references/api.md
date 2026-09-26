# API between client and server

Read this when a task adds or changes an endpoint, or changes client code that calls one. It applies to REST, RPC, and GraphQL, and to backend-only APIs too. Contracts in general are covered in `architecture.md` section 9.

## 1. One contract, shared types

- Define the contract once, as an OpenAPI schema, a tRPC router, a GraphQL schema, or a shared schema library like zod. Generate or infer the client's types from it. Never hand-write the same types on both sides.
- Parse responses at the client boundary too. The contract says what should arrive, and parsing proves it did. It also catches a client and server that have drifted apart.

## 2. Old clients talk to new servers

Users leave tabs open for days, and mobile apps update late. During every deploy, old clients call the new server, and in the middle of a rollout new clients may call an old server.

- Make changes additive. Add fields and endpoints. Never rename or remove a field, or change its type or meaning, in one step.
- A new required request field breaks old clients. Add it as optional with a server default first.
- To remove something, stop using it in the client and ship that. Wait until old clients are gone, or force a refresh, and only then remove it from the server.
- Clients ignore unknown fields, and handle unknown enum values without crashing.

## 3. The server validates everything

- Validate and authorize every request on the server, whatever the client already checked. Client checks exist only to help the user.
- Authorize per record, not just per route. Check that this user may read or change this specific record, or anyone can fetch other users' data by changing an ID.
- Never trust IDs of other users, prices, roles, or totals sent by the client. Look them up or recompute them on the server.

## 4. One error format

- Use one error shape across the API, such as RFC 9457 problem details. It should carry a machine-readable code, a short human title, and for validation errors, a list of field errors with the field path and message.
- Use status codes consistently. 400 or 422 for bad input (pick one), 401 for not logged in, 403 for not allowed, 404 for not found, 409 for a conflict, 429 for rate limited, and 5xx for server faults.
- The client maps error codes to messages the user understands. It never shows raw server messages or stack traces, and the server never puts internal details in an error.
- Handle 401 in one place on the client, by refreshing the session or sending the user to log in, not separately in every call.

## 5. Safe to repeat

- Networks retry and users double-click. Every request that creates something or moves money carries an idempotency key, generated once per user action. The server stores the result for that key and returns the same result on repeats.
- GET, PUT, and DELETE are safe to repeat by design. Keep them that way. A GET never changes state.

## 6. Fetching well

- Avoid chains of requests that each wait on the previous one when they don't need to. Fetch in parallel. When one screen needs several resources, give it one endpoint shaped for that screen (a backend for frontend).
- Don't call an endpoint once per list item. Offer a batch endpoint or a way to include related data.
- Paginate every list endpoint from the start. Use cursor pagination for data that changes. Offset pagination is fine for small, stable data. Skip total counts unless the UI needs them, since they're expensive.
- Return what the client needs, not the whole database row. Never send fields the user shouldn't see, like password hashes or internal flags, and rely on the client to hide them.

## 7. Caching and freshness

- After a change, refresh or update every cached query that shows the changed data. A list that stays stale after a save is a classic bug.
- Show an update before the server confirms it (an optimistic update) only when failure is rare and easy to undo. On failure, roll it back and tell the user.
- Use HTTP caching headers (`Cache-Control`, `ETag`) for data that's safe to cache. Never let a shared cache store a user-specific response.

## 8. Wire formats

- Send times as ISO 8601 in UTC, like `2026-09-27T04:12:00Z`. Convert to local time only for display. A date with no time, like a birthday, is a plain `YYYY-MM-DD`, not midnight UTC.
- Send money as integer minor units with a currency code (`{ "amount": 1999, "currency": "USD" }`) or as a decimal string. Never as a float.
- Send IDs as strings, even when they're numeric. JavaScript loses precision on integers above 2^53.
- Pick one field naming style for the whole API, camelCase or snake_case, and stick to it.

## 9. Real-time updates

- Start with polling if it's good enough. Use server-sent events when only the server pushes. Use WebSockets only when both sides send often.
- Every live connection drops eventually. Reconnect with backoff, then refetch or resume from a cursor so no updates are lost.

## 10. Limits and timeouts

- Client requests have a timeout and can be cancelled, for example when the user navigates away.
- Rate-limit endpoints that are expensive or easy to abuse, like login, signup, and search. Return 429 with a `Retry-After` header.
- Cap request body size and page size on the server.

## Verify

- Call the endpoint for real, including the failure paths, like invalid input, no login, another user's record, and a repeated submit with the same idempotency key.
- Confirm the current client still works against the new server. If the change could break an older client, show why it can't, or go back and make it additive.
- Cover the main user flow once, end to end, through the real client and the real server.

## Review

Look hardest at changes that break older clients, missing server-side authorization per record, and submits that aren't safe to repeat. A change that breaks older clients and missing authorization are each at least high.
