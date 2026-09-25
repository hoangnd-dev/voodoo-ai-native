# US-01 Establish Guest Identity

**Epic:** Guest identity  
**Priority:** Must  
**Actor:** Guest player

## Statement

As a guest player, I want to submit and retain a valid display name so that I can enter the lobby without registration.

## Preconditions

Guest is on name screen, or has an existing saved guest name. No room seat is required.

## Traceability

**Rules:** ID-01, ID-02, ID-03, ID-05, ID-06, ID-08.  
**Mock:** [HF-01 Enter name](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_01_enter_name/code.html); lobby identity in [HF-02](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_02_lobby_ch_a_c_ph_ng/code.html).

## Acceptance criteria

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

## Dependencies and boundaries

**Dependencies:** Guest identity service; browser cookie storage.  
**Out of scope:** Registration and login.
