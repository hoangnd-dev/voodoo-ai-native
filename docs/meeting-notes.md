# Phase 1 — Build the Core Caro Game

## Player

- **Guest join with name**
  - Guest can join by providing a player name.
  - No account is required.

- **User identity**
  - Generate a unique `userId` for each guest.
  - Store the `userId` in a browser cookie.
  - Use the `userId` to identify the player when they reconnect or refresh.
  - Persist the player's `userId` and name during the game session.

## Game Engine

- **15 × 15 board**
  - The board contains 225 cells.
  - Each cell can contain `X`, `O`, or remain empty.

- **Players and turns**
  - The first player is `X`.
  - The second player is `O`.
  - Players alternate turns: `X → O → X → O → ...`.
  - A turn changes only after a valid move.

- **Valid moves**
  - A player can only place a symbol on an empty cell.
  - A player can only move during their own turn.
  - Moves are not allowed after the game has ended.
  - Invalid moves must be rejected by the game engine/API.

- **Win condition**
  - A player wins when they have 5 consecutive symbols.
  - Check horizontal, vertical, and diagonal lines.

- **Draw condition**
  - If all 225 cells are filled and nobody has won, the game ends in a Draw.

- **Game states**
  - `WAITING`
  - `PLAYING`
  - `WIN`
  - `DRAW`

## Lobby

- **List available rooms**
  - Display rooms that guests can currently join.
  - Show basic room information needed to identify a room.

- **Create room**
  - A guest can create a room.
  - The creator becomes the **room owner**.
  - The room owner plays as `X`.
  - A newly created room starts in `WAITING` state.

- **Join room**
  - A guest can join an available room.
  - Each room supports a maximum of **2 players**.
  - The second player plays as `O`.
  - No passcode is required.

- **Room owner management**
  - The room owner controls when a game starts.
  - The room owner can start a new game after a Win or Draw.

- **Room lifecycle**
  - Remove a room when no players remain.
  - Rooms are kept in memory only.
  - No room persistence is required.
  - All rooms are lost when the server restarts.

## Gameplay

- **Game API**
  - API for creating and joining rooms.
  - API for starting a game.
  - API for making moves.
  - API for leaving a room.
  - API enforces game rules and validates moves.
  - API returns the current game state.

- **Game UI**
  - Display the 15 × 15 board.
  - Display player names and symbols.
  - Display the current player's turn.
  - Display the game result.
  - Display available actions based on the current game state.

- **Start Game**
  - The room owner must explicitly click **Start Game**.
  - The game does not start automatically when the second player joins.
  - The game can start only when **2 players** are in the room.

- **Play Game**
  - Players place their symbol on the board during their turn.
  - The UI updates after every valid move.
  - The game ends when there is a Win or Draw.

- **New Game**
  - After a Win or Draw, the game remains in its final state.
  - The game does **not** restart automatically.
  - The room owner must explicitly click **New Game**.
  - **New Game** is available only when **2 players** are still in the room.
  - Starting a New Game resets the board.
  - `X` goes first again.

- **Leave Room**
  - A player can leave the room at any time.
  - The player's membership is removed from the room.
  - If no players remain, the room is removed from the lobby.

## Out of Scope for MVP

- Sign up / account registration
- Sign in / authentication
- 20-second turn timer
- Timeout-based loss
