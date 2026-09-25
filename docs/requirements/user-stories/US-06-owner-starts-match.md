# US-06 Owner Starts Match

**Epic:** Match lifecycle  
**Priority:** Must  
**Actor:** Owner

## Statement

As Owner, I want to start a full waiting room so that both players can begin with X's turn.

## Preconditions

Caller is Owner; room is waiting; both seats are filled.

## Traceability

**Rules:** LC-01, LC-02, LC-03, LC-04, EC-09, EC-10, EC-11.  
**Mock:** [HF-05 Owner ready](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_05_ch_ph_ng_s_n_s_ng_start/code.html); [HF-06 Guest waiting](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_06_kh_ch_ch_start/code.html).

## Acceptance criteria

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

## Dependencies and boundaries

**Dependencies:** Two-seat membership; canonical room snapshot.  
**Out of scope:** Auto-start on second-player join.
