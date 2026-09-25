# US-04 Join Waiting Room as O

**Epic:** Lobby and membership  
**Priority:** Must  
**Actor:** Guest player

## Statement

As a guest player, I want to join an available waiting room so that I can play as O against its Owner.

## Preconditions

Guest has usable identity, is not seated, and has a lobby room row to select.

## Traceability

**Rules:** RM-04, RM-07, RM-08, RM-09, LC-01.  
**Mock:** [HF-03 Listed lobby](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_03_lobby_m_t_ph_ng/code.html); [HF-05 Owner ready](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_05_ch_ph_ng_s_n_s_ng_start/code.html); [HF-06 Guest waiting](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_06_kh_ch_ch_start/code.html).

## Acceptance criteria

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

## Dependencies and boundaries

**Dependencies:** Atomic room seat assignment; current room state.  
**Out of scope:** Replacing a player who leaves during or after play.
