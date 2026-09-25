# Voodoo AI Native — Caro Online

A web app for **Caro (Gomoku)**. Two guests enter a name, meet in a lobby, and play on a **15×15** board. The first player is **X**, the second is **O**. A player wins with **five consecutive** marks (horizontal, vertical, or diagonal). A full board with no winner is a **draw**.

There are no accounts and no room passcode. Rooms live **in memory** on the server and are lost on restart. The room **owner** (the creator) explicitly starts the first game and starts a new game after a win or draw.

## Product goal

Two browsers can create or join a room, play a legal match, see the result, and start a new game in the same room.

## Target users

Casual players who want a short match with a friend on another device. They should be able to play immediately after typing a name.

## MVP features

### 1. Guest identity

- Join with a player name. There is no registration or login.
- The server assigns a `userId`. The browser stores that id in a cookie together with the name.
- The same browser keeps the same guest when the landing page or lobby is refreshed, so the name is not typed again.

### 2. Lobby

- **Create room:** the creator is the owner and plays as X. The room starts in a waiting state.
- **List joinable rooms:** rooms that are waiting and have fewer than two players. Each entry shows enough to pick one (room id and owner name).
- **Join room:** the second player is O. A room holds at most two players. There is no passcode.
- **Leave room:** the player is removed. If the room is empty, it is deleted.

### 3. Play Caro

- 15×15 board. X moves first. Players alternate turns. A move is legal only on an empty cell, on the player's turn, and before the game has ended. The game API enforces these rules.
- **Win:** five consecutive X or O marks, horizontal, vertical, or diagonal.
- **Draw:** all 225 cells are filled and there is no winner.
- The game does not start when the second player joins. The owner clicks **Start** when two players are in the room.
- After a win or draw the board stays as it is. The owner clicks **New game** only if two players are still in the room. The board resets and X goes first again.
- The UI shows the board, both names and symbols, whose turn it is, the result, and only the actions that match the current state.

## Game rules

- The board has 225 cells. Each cell is empty, X, or O.
- X always starts a game.
- Players cannot place a mark in an occupied cell, move out of turn, or move after the game has ended.
- The game ends in a win or a draw. It does not restart by itself.
- A new game requires two players still in the room and an explicit action from the room owner.

## Player identity

Guests are the only players. The server assigns a `userId`, and the browser keeps that id and the display name in a cookie.

Refreshing the landing page or lobby keeps the same guest. That cookie is not a guarantee that a player can reclaim a mid-game seat after closing the tab or switching devices.

## Project status

This project is being developed as an AI-native workshop product. The repository uses role-specific custom agents and skills to support the software development lifecycle:

- Business analysis and requirements.
- Development planning and implementation.
- Test strategy, test cases, and automation.
- Accessibility and quality validation.

See the following files for more context:

- [`docs/product-brief.md`](docs/product-brief.md) — product vision, MVP scope, and what is explicitly out of scope.
- [`docs/meeting-notes.md`](docs/meeting-notes.md) — Phase 1 guest identity, lobby, game API, and gameplay.
- [`docs/software-architecture-document.md`](docs/software-architecture-document.md) — workshop software architecture.
- [`.github/agents/ba.agent.md`](.github/agents/ba.agent.md) — BA role.
- [`.github/agents/developer.agent.md`](.github/agents/developer.agent.md) — Developer role.
- [`.github/agents/test.agent.md`](.github/agents/test.agent.md) — Test role.
- [`.github/skills/`](.github/skills/) — repository skills for analysis, development, testing, accessibility, and automation.

## Development principles

- Prefer a complete vertical slice over many incomplete features.
- Keep game rules deterministic and independently testable.
- Enforce critical game rules in the game API. Do not trust client-side validation alone.
- Keep rooms in memory. Do not add a database or persistence across server restart without an explicit product decision.
- Preserve traceability from product decision to requirement, implementation, and test.
- Keep guest play as the only way in.
- Keep the MVP small and suitable for a short demo.
- Do not add out-of-scope features without an explicit product decision.

## Out of scope for the MVP

The following are intentionally excluded:

- Accounts, login, OAuth, email verification, and passwords.
- Room passcodes and private rooms.
- A database, or rooms that survive a server restart.
- Match history, rankings, replays, chat, undo, spectators, and an AI opponent.
- A turn timer or clock.
- Starting the game automatically when the second player joins.
- Advanced Caro rules such as blocked heads, Swap2, 3×3 restrictions, or forbidden overlines.
- Native mobile applications.
- Custom JWT or hosted auth (for example Firebase or Supabase Auth).
- A lobby of full or in-progress games, and matchmaking beyond picking a waiting room.
- A guaranteed rejoin of a mid-game seat after the tab is closed or the player switches devices.
- An ownership-transfer UI, and forfeit-by-disconnect polish.

## Technology

Rooms are in memory for this MVP. The frontend, backend, and realtime technologies are not selected in the product brief. Do not assume a framework or database from this README. Check the repository manifests and implementation files before making technology-specific changes.

## Contributing workflow

For a new feature or meaningful change:

1. Read the product brief and relevant requirements.
2. Confirm that the change is within scope.
3. Define or update acceptance criteria.
4. Create a focused implementation or test plan.
5. Implement one vertical slice at a time.
6. Add or update tests.
7. Run relevant validation commands.
8. Review the complete diff.
9. Update documentation and traceability artifacts.

Do not commit credentials, tokens, cookies, or other secrets.

## Setup harness

  ```bash
  copilot plugin marketplace add obra/superpowers-marketplace
  copilot plugin install superpowers@superpowers-marketplace
  ```
- Install caveman
  ```bash
  npx -y github:JuliusBrussee/caveman -- --only copilot --with-init
  ```
