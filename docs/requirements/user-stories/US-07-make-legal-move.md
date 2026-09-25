# US-07 Make Legal Move

**Epic:** Caro gameplay  
**Priority:** Must  
**Actor:** Player whose turn it is

## Statement

As the player whose turn it is, I want to mark an empty board cell so that play alternates legally.

## Preconditions

Room is playing; caller is seated; selected cell is on 15x15 board.

## Traceability

**Rules:** PL-01, PL-02, PL-03, PL-04, EC-12, EC-13.  
**Mock:** [HF-07 My turn](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_07_t_i_l_t_m_nh/code.html); [HF-08 Opponent turn](../../Knowledge%20Base/mockups/stitch_caro_gomoku_web_app_ui/hf_08_t_i_l_t_i_th/code.html).

## Acceptance criteria

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

## Dependencies and boundaries

**Dependencies:** Server-side move validation; board snapshot.  
**Out of scope:** Undo, pass, timers, and advanced Caro rule variants.
