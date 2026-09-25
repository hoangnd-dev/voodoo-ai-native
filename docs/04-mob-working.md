| Stage | Lead | Output (committed to repo) |
|-------|------|----------------------------|
| Requirements | BA | User stories + acceptance criteria → `docs/requirements/` |
| Design / SAD (optional) | Dev | Architecture doc, key decisions → `docs/design/` |
| Delivery planning | All | Analyse the dependency of user stories, priority |
| ☕ Break | — | — |
| Build (spec-driven loop) | Dev + Test | Working code + tests |
| Polish + demo prep | All | Stable build, demo script, token log |

## Requirements

```bash
/caveman
Use skill ba-consultative-elicitation.
Read only the knowledge base: product brief, mock HTML, and business rules.
When they conflict, behavior comes from the business rules, scope from the product brief, and visible actions from the mocks.
Write a one-page check: confirmed facts, contradictions, and blocking questions only.
If nothing blocks a user story, write "No blocking gaps."
Save docs/requirements/source-check.md

/caveman
Use skill ba-functional-decomposition.
Decompose only behavior already in the business rules, the product brief, and docs/requirements/source-check.md.
Cite rule IDs. Keep the product brief's out-of-scope list out of the MVP.
Non-blocking notes in source-check.md stay settled.
Save docs/requirements/functional-decomposition.md

/caveman
Use skill ba-user-story-authoring-review.
Follow that skill's output contract.
Write the user stories for today's MVP. You decide how to slice them.
Each story: statement, preconditions, Gherkin with one happy path and at least one negative or state case.
Cite rule IDs and the mock screen on each story.
Do not copy the rule tables. Do not add behavior the sources mark out of scope.
Save one Markdown file per story under docs/requirements/user-stories/.

/caveman
Use skill ba-agile-scrum-product-owner.
Input: docs/requirements/user-stories/
Order the stories by dependency.
The demo path must reach the goal in the product brief.
Save docs/requirements/delivery-plan.md with columns: order, story id, depends on, why this order, on the demo path (yes/no).
```
## Design / SAD
## Delivery planning
## Build (spec-driven loop)
