# Caro Source Check

Date: 2026-09-25

Sources reviewed: [product brief](../Knowledge%20Base/product-brief.md), [business rules](../Knowledge%20Base/caro-business-rules.md), and all HTML mockups under [Caro mockups](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/).

## Confirmed facts

- Product is a guest-only Caro/Gomoku web app for two players. No accounts, login, passcodes, database-backed rooms, history, chat, AI, timers, or auto-start.
- Guest enters a non-blank display name. Server assigns `userId`; browser keeps guest name in a cookie. Same display name may belong to different guests.
- Owner creates room and plays X. Second player joins as O. Room capacity is two. Only waiting rooms with fewer than two players appear in lobby.
- Owner must explicitly start game after both seats fill. Join does not start game. Board is 15x15; X starts; players alternate on empty cells only.
- Win requires five or more consecutive marks horizontally, vertically, or diagonally. Full board without a win is draw. Win check precedes draw check. Finished board remains visible and locked.
- Owner alone may start a game or start a new game after win/draw, and only while both seats remain filled. New game resets board and keeps owner as X.
- Leave is the only room exit action. Owner leaving closes room for remaining player; O leaving before start returns room to lobby; O leaving during/after game leaves owner with no moves or restart action.
- Mockups visibly cover: name entry, empty/listed lobby, owner waiting, both players waiting for Start, each turn, owner win, guest loss, owner draw, and guest draw. Visible actions match the stated owner/guest permissions: `Create room`, `Join`, `Start game`, `New game`, and `Leave room`.

## Contradictions

- [HF-03](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_03_lobby_m_t_ph_ng/code.html) is titled "Lobby, one room" but visibly contains two room rows: `4F2A` and `8B1C`. Business rule RM-02 permits multiple joinable rooms, so screen title/sample data conflict; behavior is not otherwise contradicted.
- Mockups use `Host` while the business rules use `Owner` for the creator role. Meaning appears identical, but product terminology is inconsistent.

## Blocking questions

No blocking gaps.
