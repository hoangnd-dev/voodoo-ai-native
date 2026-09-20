# Voodoo AI Native — Caro Online

A Vietnamese-language online **Caro (Gomoku)** game for two players. Players can create or join a shared room from different browsers, take turns on a 15×15 board, and win by placing five or more consecutive marks in a horizontal, vertical, or diagonal line.

Guests can start playing immediately. Authentication is optional and must never block gameplay.

## Product goal

Deliver a thin, end-to-end MVP that can be demonstrated in one workshop day:

- Two players join the same room.
- Players can join as guests or use optional email/password authentication.
- Players take turns on a synchronized 15×15 board.
- The game detects wins, draws, and invalid moves.
- The UI presents game status and results in Vietnamese.
- The room owner can start a new game after a win or draw.

## MVP features

### 1. Play Caro

- 15×15 board with 225 cells.
- Player X moves first; player O moves second.
- Players alternate turns.
- A move is valid only when the selected cell is empty and it is the player's turn.
- Five or more consecutive marks in a horizontal, vertical, or diagonal line wins.
- A full board with no winner is a draw.
- The board is locked after a win, draw, or timeout.
- The room owner can explicitly start a new game.
- A new game clears the board and makes X move first again.

### 2. Online rooms

- Create a room.
- Join an available room.
- Maximum of two players per room.
- The room creator becomes the owner and plays as X.
- The second player plays as O.
- The game state is synchronized between both players.
- The room owner explicitly starts the first game.
- Empty rooms are removed when no players remain.

### 3. Optional authentication

- Guests can play without registering or logging in.
- Guests enter a display name.
- Signed-in users use their account name in the room.
- Email/password authentication is optional.
- Login must never be required before playing.

## Game rules

- The board contains 225 cells.
- Each cell is empty, X, or O.
- X always starts.
- Players cannot place a mark in an occupied cell.
- Players cannot move out of turn.
- Players cannot move after the game has ended.
- The game ends with a win, draw, or timeout.
- The current game must not restart automatically.
- A new game requires two players and an explicit action from the room owner.

## Player identity

Guests may be assigned a unique client identity and display name. When supported by the implementation, the identity can be retained in a browser cookie so the guest can be recognized during the current participation session.

Signed-in players use their account identity and do not need to enter a separate guest name.

## Language and UX

All user-facing copy must be in Vietnamese, including:

- Navigation and page titles.
- Form labels and buttons.
- Room states.
- Turn indicators.
- Validation and error messages.
- Win, draw, and timeout messages.
- Accessibility labels.

The board should support keyboard interaction, visible focus states, readable contrast, and clear labels for each cell where applicable.

## Project status

This project is being developed as an AI-native workshop product. The repository uses role-specific custom agents and skills to support the software development lifecycle:

- Business analysis and requirements.
- Development planning and implementation.
- Test strategy, test cases, and automation.
- Accessibility and quality validation.

See the following files for more context:

- [`docs/product-brief.md`](docs/product-brief.md) — product vision, MVP scope, assumptions, and closed decisions.
- [`docs/meeting-notes.md`](docs/meeting-notes.md) — Phase 1 game, lobby, API, and gameplay requirements.
- [`.github/agents/ba.agent.md`](.github/agents/ba.agent.md) — BA role.
- [`.github/agents/developer.agent.md`](.github/agents/developer.agent.md) — Developer role.
- [`.github/agents/test.agent.md`](.github/agents/test.agent.md) — Test role.
- [`.github/skills/`](.github/skills/) — repository skills for analysis, development, testing, accessibility, and automation.

## Development principles

- Prefer a complete vertical slice over many incomplete features.
- Keep game rules deterministic and independently testable.
- Enforce critical game rules on the authoritative server or data layer; do not trust client-side validation alone.
- Preserve traceability from product decision to requirement, implementation, and test.
- Keep guest play available.
- Keep the MVP small and suitable for a five-minute demo.
- Do not add out-of-scope features without an explicit product decision.

## Out of scope for the MVP

The following are intentionally excluded unless explicitly approved:

- Mandatory login before play.
- Match history, rankings, or replays.
- Email verification or password reset.
- OAuth or social login.
- AI opponent.
- Chat, undo, spectators, or matchmaking.
- Advanced Caro rules such as blocked heads, Swap2, or 3×3 restrictions.
- Native mobile applications.
- Complex reconnect and resume behavior after refresh or tab close.

## Technology stack

The final frontend, backend, and realtime/data technologies are still to be selected. Do not assume a framework or database from this README. Check the repository manifests and implementation files before making technology-specific changes.

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
