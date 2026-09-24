# Product Brief

## Overview

A Vietnamese-language web app for **Caro (Gomoku)**. Two players join a shared room from different browsers, take turns on a **15×15** board, and win with **five or more** marks in a line (horizontal, vertical, or diagonal).

**Guests can play immediately.** Signing in is optional and must never block play.

## Goal

In one workshop day, ship a thin end-to-end slice: two people (guest and/or signed-in) can create/join a room, play a legal match, see a Vietnamese win/draw result, and start a new game in the same room.

## Target users

Casual players in Vietnam who want a short match with a friend on another device. They should not be forced to create an account.


| Identity       | How they enter a room | Name shown                                |
| -------------- | --------------------- | ----------------------------------------- |
| Guest          | Types a display name  | That display name                         |
| Signed-in user | Email + password      | Account name (no extra guest-name prompt) |




## Core features (MVP — max 3)

1. **Play Caro** — 15×15 board, alternate X/O, occupied cells locked, win/draw detection, board lock on end, Vietnamese UI, New game in the same room.
2. **Online room** — create or join by room; live sync; exactly two players; guests allowed.
3. **Remember user name** —  Guests can play without an account just provide name. Remember that name for next times



## Out of scope (explicitly NOT building today)

- Requiring login before play
- Match history, rankings, replays, move clocks
- Email verification, password reset, OAuth / social login
- AI opponent
- Chat, undo, spectators, matchmaking lobby
- Reconnect / resume after refresh or tab close (known limitation)
- Advanced Caro rules (blocked heads, swap2, 3×3 only)
- Native mobile apps



## Assumptions

- Room creator is **player X** and always moves first, including after New game.
- “Five in a row” means **five or more** consecutive marks in a straight line.
- All **user-facing copy is Vietnamese**.
- Demo uses two browsers. One seeded account is enough to show optional login.



## Non-functional (thin)


| Topic            | Rule                                                     |
| ---------------- | -------------------------------------------------------- |
| Language         | UI Vietnamese                                            |
| Access           | Play is never behind login                               |
| Capacity         | 2 players per room                                       |
| Honesty of state | Hosted DB is source of truth for turns and cells         |
| Auth             | Email/password only; email verification **disabled**     |
| Persistence      | Room state lives while the room exists; no match history |
| Refresh          | Losing the session on refresh is acceptable for MVP      |




## Tech stack (align with the harness)

Locked in `docs/software-architecture-document.md` §2:

- **Runtime:** Node.js 24, TypeScript
- **App:** Next.js App Router (same-origin UI + Route Handlers)
- **Realtime:** SSE snapshot push after HTTP commands
- **State:** in-memory (single process); authoritative server, not client
- **Auth:** email/password, optional; guest cookie + remembered display name



## Success for the 5-minute demo

1. Browser A joins as **guest** (display name) and creates a room → room code visible.
2. Browser B **logs in** (or signs up) and joins the same code **without** typing a guest name.
3. Players reach five in a row → both boards lock → Vietnamese winner message.
4. New game clears the board; X moves first.
5. Optional 15s: B logs out and can still join a later room as guest.



## Traceability


| Feature                  | Stories      | Knowledge                                    |
| ------------------------ | ------------ | -------------------------------------------- |
| Play Caro                | US-04        | `docs/knowledge/game-rules.md`               |
| Online room              | US-01, US-03 | `docs/knowledge/rooms-and-sync.md`           |
| Optional auth            | US-02        | `docs/knowledge/identity-and-auth.md`        |
| Copy / UX                | all          | `docs/knowledge/ui-copy.md`                  |
| In vs out of scope cases | all          | `docs/knowledge/edge-cases-and-decisions.md` |




## Closed decisions


| Question                      | Decision                                                |
| ----------------------------- | ------------------------------------------------------- |
| Must players have an account? | **No.** Optional login.                                 |
| Persist match history?        | **No.** Out of scope.                                   |
| Benefit of logging in?        | Account name is used in the room; no guest-name prompt. |
| Board size / win length       | 15×15; five or more in a line.                          |


