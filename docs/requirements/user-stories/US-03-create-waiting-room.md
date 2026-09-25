# US-03 Create Waiting Room

**Epic:** Lobby and membership  
**Priority:** Must  
**Actor:** Guest player

## Statement

As a guest player, I want to create a room so that another guest can join me for Caro.

## Preconditions

Guest has usable identity and is not seated in another room.

## Traceability

**Rules:** RM-01, RM-05, RM-06, RM-14.  
**Mock:** [HF-02 Empty lobby](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_02_lobby_ch_a_c_ph_ng/code.html); [HF-04 Owner waiting alone](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_04_ch_ph_ng_ch_m_t_m_nh/code.html).

## Acceptance criteria

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

## Dependencies and boundaries

**Dependencies:** Room membership state; unique room identifier.  
**Out of scope:** Room passcodes and private rooms.
