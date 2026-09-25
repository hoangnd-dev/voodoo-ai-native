# Caro Functional Decomposition

Date: 2026-09-25

## Boundary and outcome

**Product:** Caro online, two guest players per room.

**Outcome:** Two browsers create or join a room, play one legal 15x15 Caro match, see the result, and start another game in the same room.

**Actors:** Guest player, Owner, server/game authority.

**Core inputs:** Display name, create/join/leave/start/new-game actions, board-cell move.

**Core outputs:** Guest identity, joinable-room list, canonical room/game snapshot, board state, turn, result, validation/status message.

## Functional tree

```text
Caro online
  1. Establish guest identity
    1.1 Submit display name
      Feature: accept non-blank name and create guest identity
      Rules: ID-01, ID-02, ID-05
    1.2 Restore guest identity
      Feature: retain saved name on name/lobby refresh; return to name screen if unusable
      Rules: ID-03, ID-08
    1.3 Change guest identity
      Feature: allow Not you? only on name screen/lobby while not seated
      Rules: ID-06, ID-07, EC-23, EC-24

  2. Manage lobby and room membership
    2.1 List joinable rooms
      Feature: show every waiting room with fewer than two players, room id, owner name
      Rules: RM-02, RM-03, LC-10
    2.2 Create room
      Feature: create waiting room; creator becomes Owner and X; board starts empty
      Rules: RM-01, RM-05, RM-06, RM-14
    2.3 Join room
      Feature: join available waiting room as O; enforce two-seat limit and no passcode
      Rules: RM-04, RM-07, RM-08, RM-09
    2.4 Leave room
      Feature: remove current seat in any room state; delete empty room
      Rules: RM-10, RM-11, RM-12, RM-18
    2.5 Resolve departure state
      Feature: close room when Owner leaves; relist waiting room when O leaves before Start; lock remaining room after O leaves during/after game
      Rules: RM-15, RM-16, RM-17, LC-10

  3. Control match lifecycle
    3.1 Wait for both seats
      Feature: keep room waiting after O joins; expose Start only to Owner when both seats filled
      Rules: LC-01, LC-02, LC-04; visible states: RM-13, PL-10
    3.2 Start match
      Feature: move waiting room to playing, clear board, give first turn to X
      Rules: LC-02, LC-03, EC-09, EC-10, EC-11
    3.3 Finish match
      Feature: preserve finished board and lock moves after win or draw
      Rules: LC-06, PL-06, PL-07, PL-08, PL-09
    3.4 Start new match
      Feature: allow Owner to reset a win/draw only while both seats remain; retain X/O seats
      Rules: LC-07, LC-08, LC-09, EC-16, EC-17

  4. Play legal Caro moves
    4.1 Render and address board
      Feature: provide 225 cells on a 15x15 board; each cell is empty, X, or O
      Rules: PL-01
    4.2 Validate and apply move
      Feature: accept only current player's empty in-board cell during playing; alternate after valid move
      Rules: PL-02, PL-03, PL-04, EC-12
    4.3 Detect win
      Feature: detect five or more consecutive marks horizontally, vertically, or diagonally, including six or more
      Rules: PL-05, PL-06, EC-13, EC-14
    4.4 Detect draw
      Feature: return draw only when all 225 cells are filled and win check failed
      Rules: PL-06, PL-07, PL-08, EC-15, EC-16

  5. Show canonical room/game state
    5.1 Synchronize state
      Feature: update both clients after create, join, leave, start, move, and new game from server snapshot
      Rules: PL-10 and section 7 snapshot rule
    5.2 Show role and progress
      Feature: show both names/symbols, current turn while playing, result after finish, and only legal actions
      Rules: PL-10; section 7 state matrix
    5.3 Show departure outcome
      Feature: show room-closed or opponent-left status and permitted Leave action
      Rules: RM-15, RM-17, EC-18, EC-19, EC-20, EC-21
```

## MVP story backbone

| Step | Function | MVP slice | Traceability |
| --- | --- | --- | --- |
| 1 | Establish guest identity | Enter, validate, save, and restore guest name | ID-01 to ID-08 |
| 2 | Find or create room | List every joinable room; create waiting room | RM-01 to RM-03 |
| 3 | Fill room | Join as O or handle stale/full/concurrent join rejection | RM-04 to RM-09 |
| 4 | Begin match | Owner starts only with two seats; X starts | LC-01 to LC-04 |
| 5 | Play match | Server-validates moves, alternates turns, detects win/draw | PL-01 to PL-09 |
| 6 | Finish and continue | Preserve result; Owner starts new game when allowed | LC-06 to LC-10 |
| 7 | Exit safely | Leave handling by role, state, and repeated request | RM-10 to RM-18 |
| 8 | Keep both browsers aligned | Server snapshot after every state-changing action | PL-10; section 7 |

## Cross-cutting acceptance boundaries

- Server remains authority for identity, membership, lifecycle, move legality, win-before-draw evaluation, and legal actions: ID-02, RM-05 to RM-09, LC-02 to LC-09, PL-03 to PL-09.
- UI must reflect state, not expose forbidden actions: RM-13, PL-10, section 7.
- MVP room behavior is in-memory; saved guest profile/name may remain after restart, but rooms do not: product brief, ID-09, EC-21.

## Release priority

- **Must:** Functions 1.1, 1.2, 2.1 to 2.5, 3.1 to 3.4, 4.1 to 4.4, 5.1 to 5.3.
- **Later:** None defined by reviewed sources. No additional behavior added.
