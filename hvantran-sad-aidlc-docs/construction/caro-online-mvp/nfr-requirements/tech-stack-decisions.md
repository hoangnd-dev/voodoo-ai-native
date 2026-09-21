# Tech Stack Decisions — `caro-online-mvp`

## Chosen stack

| Layer | Choice | Why |
| --- | --- | --- |
| Language | TypeScript | One language for engine, routes, UI |
| Runtime | Node.js | One process, in-memory + SSE |
| App | Next.js App Router | Same-origin UI + Route Handlers |
| UI | React Server Components for pages; Client Components for board + `EventSource` | F3=A |
| Commands | Next.js Route Handlers (HTTP) | SAD command channel |
| Sync | SSE (`text/event-stream`) from a Route Handler | ADR-W8 |
| State | In-process memory (module singleton / equivalent) | SAD Q1=A |
| Auth passwords | Hash with a standard password hasher (e.g. `bcrypt` or `argon2`) | NFR-SE-02 |
| Guest cookie | HTTP cookie holding `userId` | Identity only, no board resume |
| Tests | Node test runner or Vitest for engine; HTTP/SSE tests against the Next server | F4=A |
| Hosting | `next start` or `next dev` for workshop. Single instance | NFR-SC-01 |

## Rejected for MVP

| Option | Why not |
| --- | --- |
| Java / C# / Python | Q5=A |
| Separate SPA + API origin | Breaks same-origin lock |
| WebSocket | SAD v0.3 |
| Hosted DB / Redis | In-memory lock |
| Playwright as done-gate | F4=A |
| fast-check / PBT | Q12=C |

## Code location (greenfield)

Application at workspace root, typical Next.js tree (created at Code Generation):

- `app/` pages + route handlers
- `lib/game/` engine (pure, unit-tested)
- `lib/rooms/` in-memory registry
- `lib/sync/` SSE fan-out
- `lib/auth/` optional accounts

Never put application code under `aidlc-docs/`.

## Open at Code Generation (not product forks)

- Exact Next.js major version current-stable at generation time
- `bcrypt` vs `argon2`
- Vitest vs `node:test`
- Cookie `httpOnly` / `SameSite` defaults for localhost
