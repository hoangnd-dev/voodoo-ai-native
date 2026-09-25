# Voodoo AI Native — Copilot Instructions

## Product context

This repository builds a Vietnamese-language online Caro (Gomoku) web application. Two players join the same room from different browsers or devices and play on a 15×15 board.

Read these files before making product-level decisions:

- `README.md`
- `docs/product-brief.md`
- `docs/meeting-notes.md`
- `docs/token-log.md`
- Relevant files under `docs/knowledge/`, `docs/requirements/`, and `docs/design` when they exist

Use `docs/product-brief.md` as the primary source for product vision and MVP scope. If it conflicts with `docs/meeting-notes.md`, report the conflict instead of silently choosing a decision.

## MVP rules

Preserve these decisions unless an explicit requirement changes them:

- The UI is Vietnamese.
- Guests can play without signing in.
- Authentication is optional and must never block gameplay.
- A room has a maximum of two players.
- The room creator is the owner and plays X.
- The second player plays O.
- X moves first.
- The board is 15×15.
- Five or more consecutive marks in a horizontal, vertical, or diagonal line wins.
- A full board without a winner is a draw.
- Players cannot move out of turn, use an occupied cell, or move after the game ends.
- The room owner explicitly starts the first game and any new game.
- A game must not restart automatically after a win or draw.

Do not add these features without explicit approval:

- Mandatory login.
- AI opponent.
- Chat, spectators, matchmaking, rankings, replays, or match history.
- Undo.
- OAuth, social login, email verification, or password reset.
- Advanced Gomoku rules.
- Native mobile applications.
- Complex reconnect/resume behavior outside the agreed MVP.

## Repository inspection

Before changing code:

1. Inspect the repository structure.
2. Identify the actual frontend, backend, runtime, package manager, and test framework.
3. Read the relevant manifests and configuration files.
4. Search for existing implementations and conventions.
5. Read the relevant requirements, design notes, knowledge files, and tests.
6. Confirm that the requested change is within the current scope.

Do not assume a framework or database from the product brief. The technology stack must be verified from repository files.

## Tech-stack instructions

The technology stack is documented in `.github/instructions/`. These rules are scoped by file path and apply to specific layers:

| Layer | Instructions | Scope |
|-------|--------------|-------|
| **Frontend (React + TypeScript)** | `.github/instructions/react/react-typescript.instructions.md` | `frontend/**/*.tsx`, `frontend/**/*.ts` |
| **Build tool (Vite)** | `.github/instructions/react/vite.instructions.md` | `frontend/**`, `vite.config.ts`, `.env*` |
| **Backend (FastAPI + Socket.IO)** | `.github/instructions/python-fastapi/fastapi-socketio.instructions.md` | `backend/**/*.py`, `app/**`, `pyproject.toml` |
| **Data persistence (SQLite)** | `.github/instructions/data/sqlite-persistence.instructions.md` | `backend/**`, `domain/guests/**`, `app/domain/guests/**` |

**Read the relevant instruction file before writing code in that layer.** Apply the rules and patterns documented there.

Key principles across all layers:

- **Server is authoritative:** All game rules, room state, and move validation happen on the server.
- **React renders snapshots:** Components render server state, never optimistic local state.
- **Vite proxy for local dev:** Use the proxy in `vite.config.ts` to avoid CORS; FastAPI runs on port 8000.
- **Guests in SQLite, rooms in memory:** Guest profiles persist; rooms and games are ephemeral.
- **One Uvicorn worker:** Rooms live in process memory; do not scale to multiple workers.

## Role agents

Use the custom agents according to their responsibilities:

- `.github/agents/ba.agent.md`: requirements, scope, user stories, acceptance criteria, and traceability.
- `.github/agents/developer.agent.md`: plans, architecture, implementation, debugging, refactoring, and code review.
- `.github/agents/test.agent.md`: test strategy, test cases, automation, accessibility, security, and quality gates.

The active role agent owns role-specific decisions. A BA Agent must not implement production code, a Developer Agent must not invent product requirements, and a Test Agent must not change acceptance criteria without reporting the requirement gap.

## Superpowers and skills

Custom agents define **who is working**; Superpowers or repository skills define **how the work is performed**.

- Prefer repository-specific skills when they cover the same task as a generic workflow.
- Do not run two skills with the same purpose.
- Do not duplicate requirements, plans, test cases, or reviews.
- Do not let a workflow expand the product scope.
- Skip workflow steps that provide no value for the current MVP and state why.
- If instructions conflict, follow the more specific repository instruction and report conflicts that affect scope or architecture.

## Spec-driven workflow

For each meaningful unit of work:

1. Read the relevant product and domain documentation.
2. Clarify ambiguity and confirm acceptance criteria.
3. Create or update the appropriate artifact.
4. Create a focused implementation or test plan when needed.
5. Implement one vertical slice at a time.
6. Add or update tests.
7. Run the smallest relevant validation first, followed by broader validation when practical.
8. Review the complete diff for unintended changes.
9. Update documentation and traceability.
10. Commit the completed unit of work.

Prefer simple, explicit, readable code and focused changes. Avoid unrelated refactoring and do not claim tests passed unless they were actually run.

## Testing expectations

Cover the behavior that matters for the current change. For game and room features, consider:

- Turn order and player symbols.
- Occupied-cell and out-of-turn rejection.
- Win detection in horizontal, vertical, and diagonal directions.
- Five-or-more consecutive marks.
- Draw and board locking.
- Room capacity, ownership, joining, leaving, and new-game permissions.
- Guest access and optional authentication.
- Vietnamese copy, loading/error states, keyboard access, focus visibility, and accessible board-cell labels.

Enforce critical game rules on the authoritative server or data layer; client-side validation alone is insufficient.

## Vietnamese UX

All user-facing text must be natural Vietnamese, including labels, buttons, validation messages, room states, turn indicators, results, errors, toasts, and accessibility labels. Use consistent, clear Vietnamese copy across the application. Refer to `docs/product-brief.md` and any i18n file for term definitions and translations.

## Commit-based AI usage logging

Treat each Git commit as one completed unit of work for workshop reporting.

Before creating a commit for AI-assisted work:

1. Create or update the local, untracked `.ai-worklog.json` file.
2. Record the active custom agent and relevant skill/workflow.
3. Record the focused context files or paths used.
4. Record why AI assistance was needed.
5. Record the concrete token-efficiency optimization used.
6. Run the relevant tests or validation.
7. Commit the implementation normally.

Example `.ai-worklog.json`:

```json
{
  "agent": "Developer Agent + create-development-plan",
  "context": "docs/product-brief.md, src/game/win-detector.ts, src/game/win-detector.test.ts",
  "reason": "Implement five-or-more-in-a-row detection for the Caro MVP.",
  "optimization": "Used scoped file context and reused accepted game rules; avoided a repository-wide scan."
}
```

The `.githooks/post-commit` hook reads this file and appends a row to `docs/token-log.md` after the commit. It then removes `.ai-worklog.json`.

Do not commit `.ai-worklog.json`; it is ignored by `.gitignore`.

The hook records commit evidence and work metadata, but it cannot determine the actual AIC or raw token count. Never invent an AIC/token number. Record AIC manually from the GitHub Copilot usage dashboard in your IDE.

Do not make the post-commit hook create another commit automatically. Review the generated `docs/token-log.md` entry and include it in the next normal commit/push.

## Security

- Never commit credentials, tokens, cookies, private keys, or secrets.
- Do not log passwords or authentication tokens.
- Validate client-controlled input.
- Do not trust client-provided player symbols, room ownership, or authorization.
- Avoid exposing unnecessary user information.
- Use safe, actionable error messages without leaking implementation details.

## Response contract

For implementation tasks:

1. State your understanding of the change.
2. Identify the active role and relevant files.
3. Mention ambiguity or requirement conflicts.
4. Give a concise plan for multi-file work.
5. Implement only the approved scope.
6. Summarize changed files.
7. Report actual validation commands and results.
8. List assumptions, limitations, and follow-up work.
