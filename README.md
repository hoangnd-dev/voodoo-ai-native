# Voodoo AI Native — Caro Online

A web app for **Caro (Gomoku)**. Two guests enter a name, meet in a lobby, and play on a **15×15** board. The first player is **X**, the second is **O**. A player wins with five consecutive marks in a horizontal, vertical, or diagonal line.

There are no accounts and no room passcode. Rooms live **in memory** on the server and are lost on restart. The room **owner** explicitly starts the first game and starts a new game after a win or draw.

## Product goal

Two browsers can create or join a room, play a legal match, see the result, and start a new game in the same room.

## Target users

Casual players who want a short match with a friend on another device. They should be able to play immediately after typing a name.

## MVP features

### 1. Guest identity

- Join with a player name. There is no registration or login.
- The server assigns a `userId`.
- The browser stores that id and the display name in a cookie.
- The same browser keeps the same guest when the landing page or lobby is refreshed.

### 2. Lobby

- **Create room:** the creator is the owner and plays as X. The room starts in a waiting state.
- **List joinable rooms:** show waiting rooms with fewer than two players.
- **Join room:** the second player is O. A room holds at most two players. There is no passcode.
- **Leave room:** the player is removed. If the room is empty, it is deleted.

### 3. Play Caro

- 15×15 board with 225 cells.
- X moves first and players alternate turns.
- A move is legal only on an empty cell, on the player's turn, and before the game ends.
- The game API enforces these rules.
- Five consecutive marks horizontally, vertically, or diagonally win.
- A full board with no winner is a draw.
- The owner clicks **Start** when two players are in the room.
- After a win or draw, the board stays as it is.
- The owner clicks **New game** only when two players remain. The board resets and X goes first again.

## Project status

This project is being developed as an AI-native workshop product. The repository uses role-specific custom agents and skills for:

- Business analysis and requirements.
- Development planning and implementation.
- Test strategy, test cases, and automation.
- Accessibility and quality validation.

See:

- [`docs/product-brief.md`](docs/product-brief.md)
- [`docs/meeting-notes.md`](docs/meeting-notes.md)
- [`docs/software-architecture-document.md`](docs/software-architecture-document.md)
- [`.github/agents/ba.agent.md`](.github/agents/ba.agent.md)
- [`.github/agents/developer.agent.md`](.github/agents/developer.agent.md)
- [`.github/agents/test.agent.md`](.github/agents/test.agent.md)
- [`.github/skills/`](.github/skills/)

## Development principles

- Prefer a complete vertical slice over many incomplete features.
- Keep game rules deterministic and independently testable.
- Enforce critical game rules in the game API; do not trust client-side validation alone.
- Keep rooms in memory for the MVP.
- Preserve traceability from product decision to requirement, implementation, and test.
- Keep the MVP small and suitable for a short demo.
- Do not add out-of-scope features without an explicit product decision.

## Out of scope for the MVP

- Accounts, login, OAuth, email verification, and passwords.
- Room passcodes and private rooms.
- A database or rooms that survive a server restart.
- Match history, rankings, replays, chat, undo, spectators, or an AI opponent.
- A turn timer or clock.
- Automatically starting the game when the second player joins.
- Advanced Caro rules such as blocked heads, Swap2, 3×3 restrictions, or forbidden overlines.
- Native mobile applications.
- Complex reconnect/resume behavior outside the agreed MVP.

## Technology

Rooms are in memory for this MVP. The frontend, backend, and realtime technologies are selected from the implementation rather than assumed from the product brief. Check repository manifests and source files before making technology-specific changes.