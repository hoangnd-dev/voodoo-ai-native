# Product Brief

## Overview

A Vietnamese-language web app for **Caro (Gomoku)**. Two players join a shared room from different browsers, take turns on a **15×15** board, and win with **five or more** marks in a line (horizontal, vertical, or diagonal).

**Every player is a guest.** No accounts, sign-up, or login.

## Goal

In one workshop day, ship a thin end-to-end slice: two guests can create/join a room, play a legal match, see a Vietnamese win/draw result, and start a new game in the same room.

## Target users

Casual players in Vietnam who want a short match with a friend on another device. They enter a display name and play.


| Identity | How they enter a room | Name shown        |
| -------- | --------------------- | ----------------- |
| Guest    | Types a display name  | That display name |




## Core features (MVP — max 3)

1. **Play Caro** — 15×15 board, alternate X/O, occupied cells locked, win/draw detection, board lock on end, Vietnamese UI, New game in the same room.
2. **Online room** — create or join by room; live sync; exactly two players; guests allowed.
3. **Remember user name** — guest types a display name; the app remembers that name for the next visit.



## Out of scope (explicitly NOT building today)

- Accounts, sign-up, sign-in, logout
- Email verification, password reset, OAuth / social login
- Match history, rankings, replays, move clocks
- AI opponent
- Chat, undo, spectators, matchmaking lobby
- Reconnect / resume after refresh or tab close (known limitation)
- Advanced Caro rules (blocked heads, swap2, 3×3 only)
- Native mobile apps



## Assumptions

- Room creator is **player X** and always moves first, including after New game.
- “Five in a row” means **five or more** consecutive marks in a straight line.
- All **user-facing copy is Vietnamese**.
- Demo uses two browsers, both as guests.



## Non-functional (thin)


| Topic            | Rule                                                     |
| ---------------- | -------------------------------------------------------- |
| Language         | UI Vietnamese                                            |
| Access           | Guest display name only; no login                        |
| Capacity         | 2 players per room                                       |
| Honesty of state | Server memory is source of truth for turns and cells     |
| Persistence      | Guest id + display name in SQLite; rooms in memory only  |
| Refresh          | Losing the session on refresh is acceptable for MVP      |




## Tech stack (align with the harness)

Locked in [`docs/superpowers/specs/2026-09-25-caro-online-architecture-design.md`](superpowers/specs/2026-09-25-caro-online-architecture-design.md) §3:

- **Backend:** Python 3.12+, FastAPI, Uvicorn (single instance)
- **Realtime:** Socket.IO (`room_snapshot` after game events)
- **Frontend:** React + TypeScript (Vite SPA); Node 24 for tooling (`.envrc`)
- **Rooms:** in-memory on the API process; authoritative server, not client
- **Guests:** SQLite stores `userId` + display name; cookie + `localStorage` on the browser
- **Auth:** none



## Success for the 5-minute demo

1. Browser A enters a **guest** display name and creates a room → room code visible.
2. Browser B enters a **guest** display name and joins the same code.
3. Players reach five in a row → both boards lock → Vietnamese winner message.
4. New game clears the board; X moves first.
5. Optional: close a browser, reopen, display name is already filled in.



## Traceability


| Feature                  | Stories      | Knowledge                                    |
| ------------------------ | ------------ | -------------------------------------------- |
| Play Caro                | US-04        | `docs/knowledge/game-rules.md`               |
| Online room              | US-01, US-03 | `docs/knowledge/rooms-and-sync.md`           |
| Remember guest name      | —            | `docs/knowledge/identity-and-auth.md`        |
| Copy / UX                | all          | `docs/knowledge/ui-copy.md`                  |
| In vs out of scope cases | all          | `docs/knowledge/edge-cases-and-decisions.md` |




## Closed decisions


| Question                      | Decision                                                |
| ----------------------------- | ------------------------------------------------------- |
| Must players have an account? | **No.** Guests only. No login.                          |
| Persist match history?        | **No.** Out of scope.                                   |
| What is remembered?           | Guest display name (and guest id) for the next visit.  |
| Board size / win length       | 15×15; five or more in a line.                          |


