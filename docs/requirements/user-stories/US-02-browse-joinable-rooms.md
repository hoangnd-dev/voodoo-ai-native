# US-02 Browse Joinable Rooms

**Epic:** Lobby and membership  
**Priority:** Must  
**Actor:** Guest player

## Statement

As a guest player, I want to see every room I can join so that I can choose a waiting opponent.

## Preconditions

Guest has usable identity and is not seated in a room.

## Traceability

**Rules:** RM-02, RM-03, LC-10.  
**Mock:** [HF-02 Empty lobby](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_02_lobby_ch_a_c_ph_ng/code.html); [HF-03 Listed lobby](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_03_lobby_m_t_ph_ng/code.html).

## Acceptance criteria

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

## Dependencies and boundaries

**Dependencies:** Room state source.  
**Out of scope:** Lobby matchmaking beyond selecting a listed waiting room.
