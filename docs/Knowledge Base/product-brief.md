# Product Brief

## Overview

A web app for **Caro (Gomoku)**. Two guests enter a name, meet in a lobby, and play on a **15×15** board. The first player is **X**, the second is **O**. A player wins with **five consecutive** marks in a horizontal, vertical, or diagonal line.

No accounts. No passcode. Rooms live **in memory** on the server (lost on restart). The room **owner** (creator) explicitly starts the first game and starts a new game after a win or draw.

**Goal today:** two browsers can create/join a room, play a legal match, see the result, and start a new game in the same room.

## Target users

Casual players who want a short match with a friend on another device. They should play immediately after typing a name.

## Core features (MVP — max 3)

1. **Guest identity**
  - Join with a **player name** (no registration / login).
  - Server assigns a `userId`; the browser stores it in a **cookie** with the name.
  - Same browser keeps the same guest on refresh of the landing/lobby (name is not retyped).
2. **Lobby**
  - **Create room:** creator is **owner** and **X**; room starts **waiting**.
  - **List joinable rooms** (waiting, fewer than 2 players): enough info to pick one (room id + owner name).
  - **Join room:** second player is **O**; max **2** players; **no passcode**.
  - **Leave room:** player is removed; if the room is empty, **delete** it.
3. **Play Caro**
  - **15×15** board; X first; alternate turns; only empty cells; no moves off-turn or after the game has ended (enforced by the **game API**).
  - **Win:** 5 consecutive X or O (H / V / diagonal). **Draw:** all 225 cells filled, no winner.
  - Game does **not** start when the second player joins. **Owner clicks Start** when 2 players are present.
  - After win/draw the board **stays**; **owner clicks New game** only if 2 players are still in the room. Board resets, **X goes first** again.
  - UI: board, both names + symbols, whose turn, result, actions that match the current state.



## Out of scope (explicitly NOT building today)

- Accounts, login, OAuth, email verification, passwords
- Room passcode / private rooms
- Database; rooms surviving **server restart**
- Match history, rankings, replays, chat, undo, spectators, AI opponent
- **Turn timer** / clocks (see notes — mentioned once, not a feature)
- Auto-start when player 2 joins
- Advanced Caro rules (blocked heads, swap2, 3×3, forbidden overline)
- Native mobile apps
- Custom JWT / hosted Auth (Firebase/Supabase Auth) — not needed; guests only
- Lobby of **full or in-progress** games; matchmaking beyond “pick a waiting room”
- Guaranteed rejoin of a **mid-game seat** after tab close / another device (cookie identity ≠ full reconnect)
- Ownership transfer UI, forfeit-by-disconnect polish

## Tech stack

- **Frontend:** React + TypeScript, using a Vite SPA. Realtime updates use `socket.io-client`.
- **Backend:** Python 3.12+ with FastAPI. Realtime communication uses Socket.IO via `python-socketio`, mounted as an ASGI application and served with Uvicorn.
- **Data/storage:** In-process memory for rooms and active games; SQLite for guest profiles (`guests(id, display_name)`). The server runs as a single worker/instance for the MVP, so rooms and games are lost when the API restarts while guest profiles persist in SQLite.
