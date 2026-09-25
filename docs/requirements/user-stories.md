# Caro MVP User Stories

## Artifact context

| Field | Value |
|---|---|
| Project | Caro Online |
| Module | Guest identity, lobby, rooms, match play |
| Purpose | Sprint-ready MVP stories for two-browser legal match flow |
| Audience | Product Owner, BA, Development, QA |
| Sources | [Product brief](../Knowledge%20Base/product-brief.md), [business rules](../Knowledge%20Base/caro-business-rules.md), [source check](source-check.md), [functional decomposition](functional-decomposition.md), reviewed mockups |
| Date/status | 2026-09-25 / Draft for refinement |
| Scope | Product brief MVP only |

## Shared delivery boundaries

- Server is authority for guest identity, room membership, lifecycle, move legality, result evaluation, and legal actions.
- Room screen uses `Owner` terminology, even where mockups say `Host`.
- Lobby shows every joinable room under RM-02.
- No behavior from product brief out-of-scope list is included.
- Every story below has one happy path and one negative or state case.

---

## US-01 Establish guest identity

**Epic:** Guest identity  
**Priority:** Must  
**Actor:** Guest player

**Statement:** As a guest player, I want to submit and retain a valid display name so that I can enter the lobby without registration.

**Preconditions:** Guest is on name screen, or has an existing saved guest name. No room seat is required.

**Rules:** ID-01, ID-02, ID-03, ID-05, ID-06, ID-08.  
**Mock:** [HF-01 Enter name](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_01_enter_name/code.html); lobby identity in [HF-02](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_02_lobby_ch_a_c_ph_ng/code.html).

### Acceptance criteria

```gherkin
Scenario: Guest submits valid name
  Given guest is on name screen
  When guest submits "Minh"
  Then system assigns a userId
  And browser stores guest name with that identity
  And guest enters lobby

Scenario: Guest submits blank or spaces-only name
  Given guest is on name screen
  When guest submits a blank or spaces-only value
  Then system rejects submission
  And guest remains on name screen
  And no userId is required to proceed
```

**Dependencies:** Guest identity service; browser cookie storage.  
**Out of scope:** Registration and login.

---

## US-02 Browse joinable rooms

**Epic:** Lobby and membership  
**Priority:** Must  
**Actor:** Guest player

**Statement:** As a guest player, I want to see every room I can join so that I can choose a waiting opponent.

**Preconditions:** Guest has usable identity and is not seated in a room.

**Rules:** RM-02, RM-03, LC-10.  
**Mock:** [HF-02 Empty lobby](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_02_lobby_ch_a_c_ph_ng/code.html); [HF-03 Listed lobby](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_03_lobby_m_t_ph_ng/code.html).

### Acceptance criteria

```gherkin
Scenario: Lobby lists joinable rooms
  Given guest is in lobby
  And rooms are waiting with fewer than two players
  When lobby is displayed
  Then every joinable room is listed
  And each row shows room id and owner name

Scenario: Lobby excludes unavailable rooms
  Given a room is full, playing, finished, or deleted
  When lobby is displayed
  Then that room is not listed
```

**Dependencies:** Room state source.  
**Out of scope:** Lobby matchmaking beyond selecting a listed waiting room.

---

## US-03 Create waiting room

**Epic:** Lobby and membership  
**Priority:** Must  
**Actor:** Guest player

**Statement:** As a guest player, I want to create a room so that another guest can join me for Caro.

**Preconditions:** Guest has usable identity and is not seated in another room.

**Rules:** RM-01, RM-05, RM-06, RM-14.  
**Mock:** [HF-02 Empty lobby](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_02_lobby_ch_a_c_ph_ng/code.html); [HF-04 Owner waiting alone](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_04_ch_ph_ng_ch_m_t_m_nh/code.html).

### Acceptance criteria

```gherkin
Scenario: Guest creates room
  Given guest is not seated in a room
  When guest selects Create room
  Then system creates one waiting room
  And guest becomes Owner and X
  And board is empty and game has not started
  And room is joinable while fewer than two players are seated

Scenario: Seated guest creates another room
  Given guest already occupies a seat
  When guest selects Create room
  Then request is rejected with "You are already in a room."
  And no second room is created
```

**Dependencies:** Room membership state; unique room identifier.  
**Out of scope:** Room passcodes and private rooms.

---

## US-04 Join waiting room as O

**Epic:** Lobby and membership  
**Priority:** Must  
**Actor:** Guest player

**Statement:** As a guest player, I want to join an available waiting room so that I can play as O against its Owner.

**Preconditions:** Guest has usable identity, is not seated, and has a lobby room row to select.

**Rules:** RM-04, RM-07, RM-08, RM-09, LC-01.  
**Mock:** [HF-03 Listed lobby](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_03_lobby_m_t_ph_ng/code.html); [HF-05 Owner ready](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_05_ch_ph_ng_s_n_s_ng_start/code.html); [HF-06 Guest waiting](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_06_kh_ch_ch_start/code.html).

### Acceptance criteria

```gherkin
Scenario: Guest joins available room
  Given guest selects a waiting room with one seated Owner
  When guest selects Join
  Then guest becomes O
  And room has two seats
  And room is removed from joinable lobby list
  And game remains waiting until Owner starts it

Scenario: Join loses availability
  Given selected room is full, no longer waiting, or deleted before request is processed
  When guest selects Join
  Then request is rejected with "That room is no longer available."
  And guest remains in lobby
```

**Dependencies:** Atomic room seat assignment; current room state.  
**Out of scope:** Replacing a player who leaves during or after play.

---

## US-05 Leave room and resolve departure

**Epic:** Lobby and membership  
**Priority:** Must  
**Actor:** Seated guest player

**Statement:** As a seated guest, I want to leave the room so that membership and remaining-player behavior stay correct in every room state.

**Preconditions:** Guest occupies a room seat. Leave is available from room screen.

**Rules:** RM-10, RM-11, RM-12, RM-15, RM-16, RM-17, RM-18.  
**Mock:** Room `Leave room` action in [HF-04](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_04_ch_ph_ng_ch_m_t_m_nh/code.html), [HF-05](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_05_ch_ph_ng_s_n_s_ng_start/code.html), and [HF-06](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_06_kh_ch_ch_start/code.html).

### Acceptance criteria

```gherkin
Scenario: O leaves waiting room
  Given O is seated in a waiting room
  When O selects Leave room
  Then O's seat is removed
  And Owner remains X
  And room returns to joinable lobby list

Scenario: O leaves during or after match
  Given O leaves while room is playing, won, or drawn
  When leave is processed
  Then Owner is not declared winner
  And room is not joinable
  And moves, Start, and New game are rejected
  And remaining player can only leave
```

**Dependencies:** Room lifecycle state; client navigation after leave.  
**Out of scope:** Ownership transfer and disconnect-forfeit winner behavior.

---

## US-06 Owner starts match

**Epic:** Match lifecycle  
**Priority:** Must  
**Actor:** Owner

**Statement:** As Owner, I want to start a full waiting room so that both players can begin with X's turn.

**Preconditions:** Caller is Owner; room is waiting; both seats are filled.

**Rules:** LC-01, LC-02, LC-03, LC-04, EC-09, EC-10, EC-11.  
**Mock:** [HF-05 Owner ready](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_05_ch_ph_ng_s_n_s_ng_start/code.html); [HF-06 Guest waiting](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_06_kh_ch_ch_start/code.html).

### Acceptance criteria

```gherkin
Scenario: Owner starts full room
  Given Owner and O are seated
  And room status is waiting
  When Owner selects Start game
  Then room status becomes playing
  And board is empty
  And X has first turn
  And both clients show playing state

Scenario: O requests Start
  Given O is seated in a waiting room
  When O requests Start game
  Then request is rejected
  And room remains waiting
  And snapshot is unchanged
```

**Dependencies:** Two-seat membership; canonical room snapshot.  
**Out of scope:** Auto-start on second-player join.

---

## US-07 Make legal move

**Epic:** Caro gameplay  
**Priority:** Must  
**Actor:** Player whose turn it is

**Statement:** As the player whose turn it is, I want to mark an empty board cell so that play alternates legally.

**Preconditions:** Room is playing; caller is seated; selected cell is on 15x15 board.

**Rules:** PL-01, PL-02, PL-03, PL-04, EC-12, EC-13.  
**Mock:** [HF-07 My turn](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_07_t_i_l_t_m_nh/code.html); [HF-08 Opponent turn](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_08_t_i_l_t_i_th/code.html).

### Acceptance criteria

```gherkin
Scenario: Current player marks empty cell
  Given room is playing
  And it is X's turn
  And selected cell is empty and on the board
  When X selects the cell
  Then cell contains X
  And turn changes to O
  And both clients receive updated board snapshot

Scenario: Invalid move is submitted
  Given selected cell is occupied, off board, out of turn, or game has ended
  When player submits the move
  Then server rejects the move
  And board and turn do not change
```

**Dependencies:** Server-side move validation; board snapshot.  
**Out of scope:** Undo, pass, timers, and advanced Caro rule variants.

---

## US-08 Resolve win or draw

**Epic:** Caro gameplay  
**Priority:** Must  
**Actor:** Server/game authority; both players observe result

**Statement:** As a player, I want the game to identify win or draw correctly so that both players can see a final, locked result.

**Preconditions:** Room is playing and a valid move has been accepted.

**Rules:** PL-05, PL-06, PL-07, PL-08, PL-09, PL-10, EC-14, EC-15, EC-16.  
**Mock:** [HF-09 Owner win](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_09_th_ng_ch_ph_ng/code.html); [HF-10 Guest loss](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_10_th_ng_kh_ch/code.html); [HF-11 Owner draw](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_11_draw_owner/code.html); [HF-12 Guest draw](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_12_draw_guest/code.html).

### Acceptance criteria

```gherkin
Scenario: Move completes five or more marks in a line
  Given accepted move creates five or more consecutive marks horizontally, vertically, or diagonally
  When server evaluates move
  Then result is win
  And board remains visible and locked
  And both clients see result and winner

Scenario: Last cell fills board without a line
  Given accepted move fills cell 225
  And win check finds no line of five or more
  When server evaluates move
  Then result is draw
  And board remains visible and locked
  And both clients see draw
```

**Dependencies:** Win evaluation before draw evaluation; result snapshot.  
**Out of scope:** Winner based on disconnect.

---

## US-09 Start new game after result

**Epic:** Match lifecycle  
**Priority:** Must  
**Actor:** Owner

**Statement:** As Owner, I want to start a new game after a win or draw so that the same two players can play again without changing seats.

**Preconditions:** Room status is win or draw; Owner and O are still seated.

**Rules:** LC-06, LC-07, LC-08, LC-09, LC-10, EC-16, EC-17.  
**Mock:** [HF-09 Owner win](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_09_th_ng_ch_ph_ng/code.html); [HF-11 Owner draw](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_11_draw_owner/code.html); guest result states [HF-10](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_10_th_ng_kh_ch/code.html) and [HF-12](../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_12_draw_guest/code.html).

### Acceptance criteria

```gherkin
Scenario: Owner starts new game after result
  Given room is won or drawn
  And both players remain seated
  When Owner selects New game
  Then board clears
  And room status becomes playing
  And X has first turn
  And Owner remains X and O remains O

Scenario: New game requested in invalid state
  Given room is playing, waiting, has one player, or caller is O
  When caller requests New game
  Then request is rejected
  And board, status, and seats remain unchanged
```

**Dependencies:** Completed result state; two-seat membership; canonical snapshot.  
**Out of scope:** Restart during play and resign.

---

## Traceability matrix

| Story | Decomposition | Rules | Mock evidence | Priority |
|---|---|---|---|---|
| US-01 | 1.1-1.3 | ID-01 to ID-08 | HF-01, HF-02 | Must |
| US-02 | 2.1 | RM-02, RM-03, LC-10 | HF-02, HF-03 | Must |
| US-03 | 2.2 | RM-01, RM-05, RM-06, RM-14 | HF-02, HF-04 | Must |
| US-04 | 2.3, 3.1 | RM-04, RM-07 to RM-09, LC-01 | HF-03, HF-05, HF-06 | Must |
| US-05 | 2.4-2.5 | RM-10 to RM-12, RM-15 to RM-18 | HF-04 to HF-06 | Must |
| US-06 | 3.1-3.2 | LC-01 to LC-04, EC-09 to EC-11 | HF-05, HF-06 | Must |
| US-07 | 4.1-4.2 | PL-01 to PL-04, EC-12, EC-13 | HF-07, HF-08 | Must |
| US-08 | 3.3, 4.3-4.4 | PL-05 to PL-10, EC-14 to EC-16 | HF-09 to HF-12 | Must |
| US-09 | 3.4 | LC-06 to LC-10, EC-16, EC-17 | HF-09 to HF-12 | Must |

## Quality and refinement notes

- Stories are vertical slices across guest/UI, server state, and observable result; no implementation tasks are defined.
- Negative/state coverage includes invalid identity, stale/full room, duplicate seating, role restrictions, departure states, invalid moves, finished boards, draw boundary, and invalid New game state.
- Main dependency chain: US-01 -> US-02/US-03 -> US-04 -> US-06 -> US-07 -> US-08 -> US-09. US-05 applies across room states.
- No unresolved blocking question remains in reviewed sources. Refinement still needs estimates and implementation contracts for snapshot shape and exact non-rule error presentation, without changing behavior.
