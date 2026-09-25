# US-05 Leave Room and Resolve Departure

**Epic:** Lobby and membership  
**Priority:** Must  
**Actor:** Seated guest player

## Statement

As a seated guest, I want to leave the room so that membership and remaining-player behavior stay correct in every room state.

## Preconditions

Guest occupies a room seat. Leave is available from room screen.

## Traceability

**Rules:** RM-10, RM-11, RM-12, RM-15, RM-16, RM-17, RM-18.  
**Mock:** Room `Leave room` action in [HF-04](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_04_ch_ph_ng_ch_m_t_m_nh/code.html), [HF-05](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_05_ch_ph_ng_s_n_s_ng_start/code.html), and [HF-06](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_06_kh_ch_ch_start/code.html).

## Acceptance criteria

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

## Dependencies and boundaries

**Dependencies:** Room lifecycle state; client navigation after leave.  
**Out of scope:** Ownership transfer and disconnect-forfeit winner behavior.
