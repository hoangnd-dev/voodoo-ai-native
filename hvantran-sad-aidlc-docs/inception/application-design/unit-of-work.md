# Unit of Work

Decomposition: **one unit**. SAD ADR-W1 modular monolith. Multi-unit split skipped on SAD approval.

## Unit: `caro-online-mvp`

| Field | Value |
| --- | --- |
| Type | Modular monolith (not independently split services) |
| Deployable | One same-origin process |
| Owns | Guest/account identity, rooms, match lifecycle, game engine, 20s timer, SSE sync, Vietnamese UI |

### Responsibilities

- Optional email/password auth that never blocks play
- Guest display name + cookie userId (identity only; no board resume on refresh)
- Create / list / join / leave rooms (max two players; owner is X)
- Owner Start Game / New Game
- Authoritative moves, win (five or more), draw, timeout
- SSE snapshot fan-out to seated clients
- In-memory state; lost on process restart

### Code organization (greenfield)

Application code at workspace root when generated. Suggested modules inside one app: `identity`, `rooms`, `game`, `sync`, `web`. Exact tree waits for NFR stack choice.

### Out of unit

History, rankings, OAuth, AI opponent, chat, multi-instance, hosted DB, WebSocket.
