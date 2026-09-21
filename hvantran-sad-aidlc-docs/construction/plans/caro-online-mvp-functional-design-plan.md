# Functional Design Plan — `caro-online-mvp`

Fill every `[Answer]:` in this file. Recommended option is **A** unless you have a reason. Do not leave tags blank.

After answers: generate `business-logic-model.md`, `business-rules.md`, `domain-entities.md`, `frontend-components.md`.

## Execution steps (after answers approved)

- [x] Analyze SAD + unit artifacts
- [x] Record answers; resolve any mix/depends follow-ups
- [x] Generate business-logic-model.md
- [x] Generate business-rules.md
- [x] Generate domain-entities.md
- [x] Generate frontend-components.md
- [x] Validate: mermaid + text alternatives; no invented stack

---

# Questions

## Question 1

Player leaves while status is Playing. What happens?

A) Leaver is removed. Opponent sees Vietnamese “đối thủ đã rời”. Game ends as abandoned (not a win). Room stays if one player remains, waiting; remaining player is still owner if they were owner, else they become owner. Empty room is deleted.

B) Leaver loses; opponent wins. Board locks as timeout-style loss.

C) Room and game stay frozen until the leaver returns (conflicts with no board resume).

D) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 2

Owner leaves while Waiting or Ready (game not started). What happens?

A) Room deleted if empty. If the other player remains, they become owner and X; room returns to Waiting.

B) Room always deleted when owner leaves, even if O is still there.

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 3

Which rooms appear in the lobby list?

A) Rooms with fewer than two seated players (joinable). Hide full rooms and rooms with an active Playing game.

B) Show all non-empty rooms; join fails if full or playing.

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 4

How does the 20-second countdown stay honest on both UIs?

A) Snapshot includes `deadlineAt` (server time). Clients tick locally. Server still fires timeout. SSE pushes the ended snapshot. Client clock is display-only.

B) Server pushes remaining-seconds every second over SSE.

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 5

On a win, what does the UI show besides the Vietnamese result?

A) Highlight the winning line cells. Board stays locked until owner New Game.

B) Result text only. No line highlight.

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 6

Guest is already seated, then logs in. What happens to the seat?

A) Same seat keeps the same userId for this participation. Display name switches to account name. No second seat.

B) Logout/login mid-room is unsupported; must leave first.

C) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 7

Illegal command (occupied cell, wrong turn, start by non-owner). What does the player see?

A) Snapshot unchanged. Vietnamese error message on the actor’s client only. Opponent gets no error event.

B) Both clients get the error text.

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 8

SSE drops for a few seconds, but the tab did not refresh. What should the client do?

A) EventSource reconnects. Client immediately GETs current snapshot, then resumes SSE. If the room/game is gone, show Vietnamese “phòng không còn” and return to lobby. This is not full match resume after refresh.

B) Treat any SSE drop like refresh: abandon the match.

C) Other (please describe after [Answer]: tag below)

[Answer]: A
