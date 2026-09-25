# Part 04 — Mob Working

> Duration: 5-7 hours

The whole team works together on one compute, driving Copilot Agent mode through the full SDLC. This is the core of the day and where most of the score is earned.

## Mob roles

- Driver — the only person typing / at the keyboard. Rotates every ~30-45 min.
- Navigator(s) — everyone else: decide direction, review AI output, catch issues.
- Role lens — BA leads requirements, Dev leads design/build, Test leads strategy/cases. Switch the custom agent (BA / Dev / Test) to match the current stage.

> Rotate the driver on a timer. It keeps everyone engaged and spreads the AI-collaboration practice.

## Suggested stages (adapt to your pace)

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

### Requirements stage: where to start

Work through these in order. Each one tells you what to do and which skill to switch the BA agent to.

1. Confirm the intent / goal / objective. Before opening any skill, agree as a mob on the business problem and the outcome you're after. Write it down in a sentence or two — this is your anchor for the session.
2. Run ideation and requirement discovery as needed. If the direction is not obvious yet, switch to the BA agent and use the relevant discovery or decomposition skill to clarify scope.
3. Draft the high-level feature list.
4. Add supporting documentation only when it materially helps clarify the product or flow.
5. Write the user story. For each in-scope feature, produce a story with acceptance criteria and a clear user outcome.
6. Add a wireframe or mockup only if it helps reduce ambiguity.

Once the user stories and any supporting docs are committed, hand off to the next stage.

## Delivery planning

Placeholder: define how the mob will sequence stories, identify dependencies, and decide what belongs on the demo path.

- Review the user story list and identify dependencies.
- Order stories by implementation risk and business value.
- Confirm the minimal path that reaches the product goal in the demo.
- Record the final order in `docs/requirements/delivery-plan.md`.

## Build

Placeholder: implement one user story at a time using a spec-driven loop.

- Create the development plan for the selected story.
- Break the work into tasks.
- Define the test plan and test cases before implementation.
- Implement the delta and keep the scope tight.
- Run targeted validation and review the result.
- Commit only when the slice is green and reviewed.

## Testing

Placeholder: define how quality gates and verification are handled during the build stage.

- Review acceptance criteria and edge cases.
- Add or update automated tests for the story.
- Validate happy paths and negative/state cases.
- Record test evidence and any follow-up defects.

## Polish + demo prep

Placeholder: stabilize the build and prepare the demonstration.

- Remove rough edges and ensure the demo path is reliable.
- Verify the app flows end-to-end.
- Prepare a short demo script and dry run it.
- Capture the token log or usage evidence for the session.

## Working agreements

- Store every artifact in the repo, not in chat. Requirements, design, dev specs, test strategy, and test cases must be committed.
- Review AI output — never merge code no one on the mob has read.
- Commit often — after every green (working) slice.
- Scope guard — if something is not in the MVP, add it to out-of-scope rather than building it.

## Token efficiency (this is scored)

- Prefer scoped context: `#file`, `#selection`, specific paths — avoid dumping the whole repo.
- Reuse harness instructions and skills instead of re-explaining rules each prompt.
- Keep a short token log (for example, `docs/token-log.md`): note major spends and what you did to reduce them.

## Definition of Done (per feature)

- [ ] Meets acceptance criteria
- [ ] Build passes
- [ ] Tests written and passing
- [ ] Artifacts updated and committed

---

⬅️ Prev: Part 03 — Harness Setup

➡️ Next: Part 05 — Presentation
