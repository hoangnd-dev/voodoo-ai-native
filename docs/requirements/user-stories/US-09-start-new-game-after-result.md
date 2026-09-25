# US-09 Start New Game After Result

**Epic:** Match lifecycle  
**Priority:** Must  
**Actor:** Owner

## Statement

As Owner, I want to start a new game after a win or draw so that the same two players can play again without changing seats.

## Preconditions

Room status is win or draw; Owner and O are still seated.

## Traceability

**Rules:** LC-06, LC-07, LC-08, LC-09, LC-10, EC-16, EC-17.  
**Mock:** [HF-09 Owner win](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_09_th_ng_ch_ph_ng/code.html); [HF-11 Owner draw](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_11_draw_owner/code.html); guest result states [HF-10](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_10_th_ng_kh_ch/code.html) and [HF-12](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_12_draw_guest/code.html).

## Acceptance criteria

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

## Dependencies and boundaries

**Dependencies:** Completed result state; two-seat membership; canonical snapshot.  
**Out of scope:** Restart during play and resign.
