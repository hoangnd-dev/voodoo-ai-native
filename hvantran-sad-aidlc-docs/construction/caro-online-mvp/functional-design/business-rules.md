# Business Rules — `caro-online-mvp`

IDs are stable for tests and traceability.

## Identity

| ID | Rule |
| --- | --- |
| BR-ID-01 | Play is never gated on an account. |
| BR-ID-02 | Guest must supply a non-empty display name before create/join. |
| BR-ID-03 | Guest receives a unique `userId`. Cookie may store it. |
| BR-ID-04 | Signed-in player uses account name. No extra guest-name prompt. |
| BR-ID-05 | Email/password only. No verification, reset, or OAuth. |
| BR-ID-06 | Sign up / log in is forbidden while seated. Actor must leave first (Q6=B). |
| BR-ID-07 | Log out while seated = leave room, then clear account session. |

## Rooms and lobby

| ID | Rule |
| --- | --- |
| BR-RM-01 | Max two seats. Creator is owner and X. Joiner is O. |
| BR-RM-02 | No passcode. |
| BR-RM-03 | `listAvailableRooms` returns rooms with fewer than two seats **and** status not Playing. Hide full rooms. Hide Playing rooms (Q3=A). |
| BR-RM-04 | Join rejected if room missing, full, or Playing. Actor-only error. |
| BR-RM-05 | Empty room is deleted immediately. |
| BR-RM-06 | Rooms live in process memory only. Restart wipes them. |

## Match lifecycle

| ID | Rule |
| --- | --- |
| BR-ML-01 | Game never auto-starts on join. |
| BR-ML-02 | `startGame` only: owner, two seated, status Ready. |
| BR-ML-03 | After Win or Draw, game stays Ended until owner `startNewGame`. |
| BR-ML-04 | `startNewGame` only: owner, two seated, result Win or Draw. Not Timeout. Not Abandoned. |
| BR-ML-05 | New Game and Start Game: empty board, X moves first, new 20s `deadlineAt`. |
| BR-ML-06 | After Abandoned, status Waiting. Next match uses `startGame` once two seated, not `startNewGame`. |
| BR-ML-07 | Timeout locks the board as Ended. New Game is not offered. Players leave or stay on the locked board. |

## Moves and engine

| ID | Rule |
| --- | --- |
| BR-GE-01 | Board 15×15. Cells empty, X, or O. Coordinates row 0–14, col 0–14. |
| BR-GE-02 | X always starts, including after New Game. |
| BR-GE-03 | Move legal iff Playing, actor's turn, empty in-range cell. |
| BR-GE-04 | Win: five or more consecutive same marks, horizontal, vertical, or either diagonal. |
| BR-GE-05 | Snapshot includes `winningCells` on Win (Q5=A). |
| BR-GE-06 | Draw: 225 filled and no win. |
| BR-GE-07 | After Win, Draw, Timeout, Abandoned: reject further marks. |
| BR-GE-08 | Illegal command: store unchanged; Vietnamese error to actor only; no opponent error event (Q7=A). |

## Timer

| ID | Rule |
| --- | --- |
| BR-TM-01 | Each turn 20 seconds. Server owns expiry. |
| BR-TM-02 | Snapshot `deadlineAt` is server time. Client countdown is display-only (Q4=A). |
| BR-TM-03 | Timeout: player whose turn it is loses. Result Timeout. `winnerSeat` = opponent. |
| BR-TM-04 | Timer resets on legal mark, Start Game, and New Game. |
| BR-TM-05 | Stale timeout (`turnId` mismatch) is ignored. |

## Leave

| ID | Rule |
| --- | --- |
| BR-LV-01 | Playing + leave: result Abandoned, not a win. Opponent copy: đối thủ đã rời (Q1=A). |
| BR-LV-02 | After abandon, one remaining: that player is owner and seat X; Waiting. |
| BR-LV-03 | Waiting/Ready + owner leave + one remaining: remaining becomes owner and X; Waiting (Q2=A). |
| BR-LV-04 | Zero players: delete room. |

## Sync and refresh

| ID | Rule |
| --- | --- |
| BR-SY-01 | After successful mutate and after timeout, SSE-push snapshot to seated clients. |
| BR-SY-02 | UI renders authoritative snapshot only. |
| BR-SY-03 | SSE drop, tab still open: reconnect, GET snapshot, resume SSE (Q8=A). |
| BR-SY-04 | If GET finds no room: Vietnamese phòng không còn; return to lobby. |
| BR-SY-05 | Full refresh / tab close: do not restore board. Cookie userId may remain. |
| BR-SY-06 | Actor-only errors never go over the room SSE channel. |

## Copy and access

| ID | Rule |
| --- | --- |
| BR-UX-01 | All user-facing strings Vietnamese, including errors and results. |
| BR-UX-02 | Board keyboard operable; visible focus; contrast; cell names/labels. |
| BR-UX-03 | Start Game / New Game controls only for the owner, and only when the matching rule allows. |
