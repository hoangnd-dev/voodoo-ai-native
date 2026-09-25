# US-08 Resolve Win or Draw

**Epic:** Caro gameplay  
**Priority:** Must  
**Actor:** Server/game authority; both players observe result

## Statement

As a player, I want the game to identify win or draw correctly so that both players can see a final, locked result.

## Preconditions

Room is playing and a valid move has been accepted.

## Traceability

**Rules:** PL-05, PL-06, PL-07, PL-08, PL-09, PL-10, EC-14, EC-15, EC-16.  
**Mock:** [HF-09 Owner win](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_09_th_ng_ch_ph_ng/code.html); [HF-10 Guest loss](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_10_th_ng_kh_ch/code.html); [HF-11 Owner draw](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_11_draw_owner/code.html); [HF-12 Guest draw](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_12_draw_guest/code.html).

## Acceptance criteria

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

## Dependencies and boundaries

**Dependencies:** Win evaluation before draw evaluation; result snapshot.  
**Out of scope:** Winner based on disconnect.
