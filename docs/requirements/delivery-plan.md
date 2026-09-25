# Caro MVP Delivery Plan

## Context

| Field | Value |
|---|---|
| Project | Caro Online |
| Goal | Two browsers create/join room, play legal match, see result, start new game |
| Input | [MVP user stories](user-stories/) |
| Audience | Product Owner, BA, Development, QA |
| Scope | Product brief MVP only |
| Confidence | High; order follows explicit story dependencies and room lifecycle rules |
| Date/status | 2026-09-25 / Draft for refinement |

## Ordered backlog

| order | story id | depends on | why this order | on the demo path (yes/no) |
|---:|---|---|---|---|
| 1 | [US-01](user-stories/US-01-establish-guest-identity.md) | None | Guest identity is required before entering lobby or creating/joining room. | yes |
| 2 | [US-02](user-stories/US-02-browse-joinable-rooms.md) | US-01 | Usable guest identity is required to display and act in lobby; room selection starts join flow. | yes |
| 3 | [US-03](user-stories/US-03-create-waiting-room.md) | US-01 | Room must exist in waiting state before another guest can join; Owner and X seat established here. | yes |
| 4 | [US-04](user-stories/US-04-join-waiting-room-as-o.md) | US-01, US-02, US-03 | O seat and two-player membership are prerequisites for Start; join also removes room from joinable list. | yes |
| 5 | [US-05](user-stories/US-05-leave-room-and-resolve-departure.md) | US-03, US-04 | Departure behavior depends on room membership and state; must be defined before lifecycle actions are allowed across states. | no |
| 6 | [US-06](user-stories/US-06-owner-starts-match.md) | US-03, US-04, US-05 | Start requires Owner, waiting status, and two seats; departure rules protect this state transition. | yes |
| 7 | [US-07](user-stories/US-07-make-legal-move.md) | US-06 | Legal moves require playing status, assigned seats, empty 15x15 board, and X's first turn. | yes |
| 8 | [US-08](user-stories/US-08-resolve-win-or-draw.md) | US-07 | Result evaluation runs after accepted move and locks finished board. | yes |
| 9 | [US-09](user-stories/US-09-start-new-game-after-result.md) | US-08, US-05 | New game requires win/draw result plus both seats still present; leave handling determines eligibility. | yes |

## Dependency notes

- US-02 and US-03 can be developed in parallel after US-01, but both must exist before US-04.
- US-05 spans waiting, playing, win, draw, refresh, and room deletion states. It is not on the primary happy-path demo, but it is a Must MVP story.
- US-06 through US-09 form match critical path: Start, legal move, result, New game.
- Server snapshot behavior applies after create, join, leave, Start, each move, and New game; it is a cross-cutting delivery dependency for US-03 through US-09.

## Demo path

US-01 -> US-03 -> US-04 -> US-06 -> US-07 -> US-08 -> US-09

US-02 supports room discovery when demo uses an existing joinable room. US-05 is validated separately through departure scenarios.

## Refinement checks

- Confirm story estimates and sprint capacity before commitment.
- Keep server-side authority for membership, lifecycle, move legality, and result evaluation.
- Preserve product brief out-of-scope boundaries during implementation.
- Use each linked story's Gherkin criteria for QA and review evidence.
