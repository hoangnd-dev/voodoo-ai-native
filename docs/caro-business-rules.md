# Caro Online — Business Rules Knowledge Base


| Field   | Value                                       |
| ------- | ------------------------------------------- |
| Product | Caro (Gomoku) web app, two guests, one room |
| Status  | Working knowledge base                      |
| Date    | 2026-09-25                                  |


---

## 1. Terms


| Term          | Meaning                                                                  |
| ------------- | ------------------------------------------------------------------------ |
| Guest         | A player with a display name and a server-assigned `userId`. No account. |
| Owner         | The guest who created the room. Always plays X while the room exists.    |
| X             | First player. Moves first in every game in that room.                    |
| O             | Second player. The guest who joins the owner's waiting room.             |
| Waiting       | Room status before the owner starts the first game. One or two players.  |
| Playing       | A game is in progress.                                                   |
| Win           | A player has five or more consecutive marks.                             |
| Draw          | All 225 cells are filled and there is no winner.                         |
| Joinable room | A waiting room with fewer than two players.                              |
| Snapshot      | The canonical room and game state the server sends to clients.           |


---

## 2. Happy path

1. Minh enters a name. The server assigns a `userId`. The browser remembers that guest.
2. Minh creates a room. He is the owner and X. The room is waiting and appears in the lobby.
3. Lan enters her name, sees that room (room id and owner name), and joins. She is O. The room leaves the lobby.
4. The game does not start on join. Minh clicks **Start**.
5. The board is empty. X moves first. The two clients take turns on empty cells.
6. Five or more in a line ends the game. The board stays. Both clients see who won.
7. Minh clicks **New game** while both are still seated. The board clears. X moves first again. Seats do not swap.

A full board with no five-in-a-row is a draw. **New game** is available to the owner in that case as well.

---

## 3. Identity


| ID    | Rule                                                                                                                                                 |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| ID-01 | Play requires a display name. There is no registration, login, password, or OAuth.                                                                   |
| ID-02 | The server assigns `userId`. The browser stores it in a cookie.                                                                                      |
| ID-03 | Refreshing the name screen or the lobby keeps the same guest. The name is not typed again.                                                           |
| ID-04 | The cookie does not restore a seat or a board after the player leaves the room page.                                                                 |
| ID-05 | A blank name, or a name that is only spaces, is rejected. The guest stays on the name screen.                                                        |
| ID-06 | Two guests may use the same display name. `userId` is the identity. X and O distinguish them on the board.                                           |
| ID-07 | Changing name is available only on the name screen or the lobby, and only when that guest is not seated. It stores a new `userId`.                   |
| ID-08 | A missing or unusable saved name sends the guest back to the name screen.                                                                            |
| ID-09 | Guest profile rows may survive a server restart. Rooms do not. A returning browser can show the saved name and still must create or join a new room. |


---

## 4. Lobby and membership


| ID    | Rule                                                                                                                                                      |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RM-01 | **Create room:** the creator is the owner and X. The room status is waiting. The board is empty. The game has not started.                                |
| RM-02 | The lobby lists only joinable rooms. Each row shows the room id and the owner name.                                                                       |
| RM-03 | A room disappears from the lobby when a second player joins, when a game starts, when the game has ended, or when the room is deleted.                    |
| RM-04 | **Join:** the joiner is O. A room holds at most two players. There is no passcode.                                                                        |
| RM-05 | One `userId` occupies one seat in one room. Create and Join are rejected while that guest is already seated. Message: `You are already in a room.`        |
| RM-06 | A second Create from the same `userId` does not open another room. Same rejection as RM-05.                                                               |
| RM-07 | A guest cannot join a room they already sit in, including from a second tab with the same cookie.                                                         |
| RM-08 | Join is rejected when the room is full, no longer waiting, or already deleted. The guest stays in the lobby. Message: `That room is no longer available.` |
| RM-09 | Two guests who join the same room at the same time: one becomes O. The other receives RM-08.                                                              |
| RM-10 | **Leave** removes that guest's seat. It is allowed in every room state.                                                                                   |
| RM-11 | When the last guest leaves, the room is deleted immediately.                                                                                              |
| RM-12 | A second Leave for a seat that is already empty succeeds and changes nothing.                                                                             |
| RM-13 | The room screen has no Create and no Join. The only way back to the lobby while a seat is held is Leave.                                                  |
| RM-14 | There is no lifetime cap on how many rooms a guest may create. They may create another only after they have left the current one.                         |


### Who remains when someone leaves


| ID    | Situation                                                                                                                  | Result                                                                                                                                                                                    |
| ----- | -------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RM-15 | The owner leaves and the other guest is still seated.                                                                      | The room closes for both. The other guest returns to the lobby. Message: `The room has closed.` There is no ownership transfer.                                                           |
| RM-16 | O leaves while the room is still waiting.                                                                                  | The owner stays as X. The room is waiting with one player and appears in the lobby again.                                                                                                 |
| RM-17 | O leaves during play, after a win, or after a draw.                                                                        | The owner stays. The room is not listed. Moves, Start, and New game are rejected. Status: `Lan left the room.` The only action is Leave. The remaining player is not declared the winner. |
| RM-18 | Refresh, tab close, or leaving the room page counts as Leave. The saved name remains. The guest must create or join again. |                                                                                                                                                                                           |


---

## 5. Match lifecycle


| ID    | Rule                                                                                                                                          |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| LC-01 | The game does not start when the second player joins.                                                                                         |
| LC-02 | **Start** is accepted only when the caller is the owner, both seats are filled, and the status is waiting.                                    |
| LC-03 | Start moves the room to playing, clears the board, and gives the first move to X.                                                             |
| LC-04 | Start is rejected for a non-owner, for a room with fewer than two players, and when a game has already started.                               |
| LC-05 | There is no restart and no resign during play. Leaving is the way out of an unfinished game.                                                  |
| LC-06 | After a win or a draw the board stays until New game or the room ends.                                                                        |
| LC-07 | **New game** is accepted only when the caller is the owner, both seats are still filled, and the status is win or draw.                       |
| LC-08 | New game moves the room to playing, clears the board, and gives the first move to X. Minh stays X. Lan stays O.                               |
| LC-09 | New game is rejected during play, while waiting, when only one player remains, and when a non-owner requests it.                              |
| LC-10 | A room that is playing, won, or drawn is never joinable, even if one seat is empty. A new opponent cannot replace a player who left mid-game. |


---

## 6. Moves, win, and draw


| ID    | Rule                                                                                                                                                                |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| PL-01 | The board is 15×15 (225 cells). Each cell is empty, X, or O.                                                                                                        |
| PL-02 | Players alternate X, O, X, O. A turn changes only after a valid move.                                                                                               |
| PL-03 | A move is valid only when the room is playing, it is that player's turn, the cell is empty, and the cell is on the board.                                           |
| PL-04 | The server rejects a move on an occupied cell, a move out of turn, a move after the game has ended, and a cell outside the board. The turn does not change.         |
| PL-05 | Win means five or more consecutive marks in one straight line: horizontal, vertical, or either diagonal. A single move that completes six or more is a win.         |
| PL-06 | The server checks for a win before it checks for a draw.                                                                                                            |
| PL-07 | If the move that fills the 225th cell also makes five or more in a line, the result is a win.                                                                       |
| PL-08 | The result is a draw only when every cell is filled and the win check failed.                                                                                       |
| PL-09 | When the game ends, the board locks. Further moves are rejected.                                                                                                    |
| PL-10 | The client shows the board, both names, both symbols, whose turn it is while playing, the result when the game has ended, and only the actions legal in that state. |


---

## 7. What each player sees


| State                    | Owner (X)                                       | Guest (O)                                                                                                      |
| ------------------------ | ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| Waiting, one player      | Empty locked board. No Start.                   | —                                                                                                              |
| Waiting, two players     | Empty locked board. **Start**.                  | Empty locked board. No Start. Status says they are waiting for the owner.                                      |
| Playing, X to move       | Empty cells accept a move.                      | Board locked.                                                                                                  |
| Playing, O to move       | Board locked.                                   | Empty cells accept a move.                                                                                     |
| Win or draw, both seated | Board stays. **New game**.                      | Board stays. No New game.                                                                                      |
| Opponent has left        | Board stays. No Start. No New game. Leave only. | Same if the owner has not already closed the room. If the owner left, this guest is back in the lobby (RM-15). |


Both clients update from the server snapshot after create, join, leave, start, each move, and new game. A manual refresh of the room page is Leave (RM-18), so it is not the way to see the opponent's move.

---

## 8. Edge cases


| ID    | Situation                                                                    | Expected result                                                                                                                                 |
| ----- | ---------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| EC-01 | Guest opens the app with a saved name.                                       | Name screen or lobby shows that name. They continue without typing it.                                                                          |
| EC-02 | Guest submits an empty name.                                                 | Stay on the name screen. No `userId` is required to proceed.                                                                                    |
| EC-03 | Two browsers use the same display name.                                      | Two `userId`s. Both may sit in one room. Symbols show who is who.                                                                               |
| EC-04 | Minh creates a room, then creates another before leaving.                    | Second create is rejected. One room exists.                                                                                                     |
| EC-05 | Minh is seated and Lan's browser, sharing his cookie, clicks Join or Create. | Rejected. He cannot be X and O.                                                                                                                 |
| EC-06 | Lan double-clicks Join, or two people join one waiting room together.        | One O. The loser stays in the lobby with `That room is no longer available.`                                                                    |
| EC-07 | Lan's lobby list is stale and the room has filled, started, or been deleted. | Join is rejected with the same message. She stays in the lobby.                                                                                 |
| EC-08 | A third guest tries to join a full, playing, or finished room.               | Rejected. That room is not on the lobby list.                                                                                                   |
| EC-09 | Owner clicks Start as O leaves.                                              | Server rechecks the seats. Start is rejected. If the owner is still alone and the room is waiting, it returns to the lobby list.                |
| EC-10 | Owner clicks Start twice.                                                    | The first start begins the game. The second is rejected.                                                                                        |
| EC-11 | O's client sends Start or New game.                                          | Rejected. The snapshot is unchanged.                                                                                                            |
| EC-12 | A player clicks two empty cells quickly.                                     | The first legal move is applied and the turn passes. The second is rejected.                                                                    |
| EC-13 | A move arrives after the opponent already won.                               | Rejected. The finished board stays.                                                                                                             |
| EC-14 | Marks form a line longer than five.                                          | Win, under PL-05.                                                                                                                               |
| EC-15 | The last empty cell completes five in a line.                                | Win, under PL-07.                                                                                                                               |
| EC-16 | The board fills with no line of five or more.                                | Draw. Owner may click New game if both remain.                                                                                                  |
| EC-17 | Owner clicks New game after O has left.                                      | Rejected. Owner sees the leave status and can only leave.                                                                                       |
| EC-18 | Owner leaves before Start.                                                   | Room closes. O returns to the lobby (RM-15).                                                                                                    |
| EC-19 | O leaves before Start.                                                       | Owner remains. Room is joinable again (RM-16).                                                                                                  |
| EC-20 | Either player refreshes the room, or closes the tab.                         | That seat is removed (RM-18). Apply RM-15, RM-16, or RM-17 from the remaining player's side.                                                    |
| EC-21 | Server process restarts.                                                     | All rooms are gone. A client still showing a board returns to the lobby. Message: `This room is no longer available.` Saved guest names remain. |
| EC-22 | Owner clicks Leave twice.                                                    | One leave is applied. The second call does not error.                                                                                           |
| EC-23 | Guest uses `Not you?` from the lobby.                                        | New `userId` and new name. They are not seated, so no room is affected.                                                                         |
| EC-24 | Guest tries to change name while seated.                                     | Unavailable. They leave first.                                                                                                                  |


---

## 9. Out of scope

These are not rules to implement and not screens to add.

- Accounts, login, OAuth, email verification, passwords, custom JWT, hosted auth
- Room passcodes and private rooms
- A database for rooms, or rooms that survive a server restart
- Match history, rankings, replays, chat, undo, spectators, an AI opponent
- Turn timers and timeout losses
- Auto-start when the second player joins
- Advanced Caro rules: blocked heads, Swap2, 3×3, forbidden overlines
- Native mobile apps
- A lobby of full or in-progress games
- Matchmaking beyond picking a waiting room
- A guaranteed return to a mid-game seat
- An ownership-transfer screen
- A winner declared because the opponent disconnected

---

