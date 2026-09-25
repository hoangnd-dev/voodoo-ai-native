# Voodoo AI Native — Caro Online

A web app for **Caro (Gomoku)**. Two guests enter a name, meet in a lobby, and play on a **15×15** board. The first player is **X**, the second is **O**. A player wins with five consecutive marks in a horizontal, vertical, or diagonal line.

There are no accounts and no room passcode. Rooms live **in memory** on the server and are lost on restart. The room **owner** explicitly starts the first game and starts a new game after a win or draw.

## Product goal

Two browsers can create or join a room, play a legal match, see the result, and start a new game in the same room.

## Target users

Casual players who want a short match with a friend on another device. They should be able to play immediately after typing a name.

## MVP features

### 1. Guest identity

- Join with a player name. There is no registration or login.
- The server assigns a `userId`.
- The browser stores that id and the display name in a cookie.
- The same browser keeps the same guest when the landing page or lobby is refreshed.

### 2. Lobby

- **Create room:** the creator is the owner and plays as X. The room starts in a waiting state.
- **List joinable rooms:** show waiting rooms with fewer than two players.
- **Join room:** the second player is O. A room holds at most two players. There is no passcode.
- **Leave room:** the player is removed. If the room is empty, it is deleted.

### 3. Play Caro

- 15×15 board with 225 cells.
- X moves first and players alternate turns.
- A move is legal only on an empty cell, on the player's turn, and before the game ends.
- The game API enforces these rules.
- Five consecutive marks horizontally, vertically, or diagonally win.
- A full board with no winner is a draw.
- The owner clicks **Start** when two players are in the room.
- After a win or draw, the board stays as it is.
- The owner clicks **New game** only when two players remain. The board resets and X goes first again.

## Project status

This project is being developed as an AI-native workshop product. The repository uses role-specific custom agents and skills for:

- Business analysis and requirements.
- Development planning and implementation.
- Test strategy, test cases, and automation.
- Accessibility and quality validation.

See:

- [`docs/product-brief.md`](docs/product-brief.md)
- [`docs/meeting-notes.md`](docs/meeting-notes.md)
- [`docs/software-architecture-document.md`](docs/software-architecture-document.md)
- [`.github/agents/ba.agent.md`](.github/agents/ba.agent.md)
- [`.github/agents/developer.agent.md`](.github/agents/developer.agent.md)
- [`.github/agents/test.agent.md`](.github/agents/test.agent.md)
- [`.github/skills/`](.github/skills/)

## Development principles

- Prefer a complete vertical slice over many incomplete features.
- Keep game rules deterministic and independently testable.
- Enforce critical game rules in the game API; do not trust client-side validation alone.
- Keep rooms in memory for the MVP.
- Preserve traceability from product decision to requirement, implementation, and test.
- Keep the MVP small and suitable for a short demo.
- Do not add out-of-scope features without an explicit product decision.

## Out of scope for the MVP

- Accounts, login, OAuth, email verification, and passwords.
- Room passcodes and private rooms.
- A database or rooms that survive a server restart.
- Match history, rankings, replays, chat, undo, spectators, or an AI opponent.
- A turn timer or clock.
- Automatically starting the game when the second player joins.
- Advanced Caro rules such as blocked heads, Swap2, 3×3 restrictions, or forbidden overlines.
- Native mobile applications.
- Complex reconnect/resume behavior outside the agreed MVP.

## Technology

Rooms are in memory for this MVP. The frontend, backend, and realtime technologies are selected from the implementation rather than assumed from the product brief. Check repository manifests and source files before making technology-specific changes.

## Setup harness

### 1. Install the AI tools

```bash
# Install GitHub Copilot CLI
curl -fsSL https://gh.io/copilot-install | bash

# Install Superpowers for Copilot CLI
copilot plugin marketplace add obra/superpowers-marketplace
copilot plugin install superpowers@superpowers-marketplace

# Install Caveman skills for Copilot
npx -y github:JuliusBrussee/caveman -- --only copilot --with-init
```

### 2. Enable the repository Git hooks

The repository includes `.githooks/post-commit`. It records each local commit as a completed unit of work and appends evidence to `docs/token-log.md`.

Run this once after cloning:

```bash
# Enable the repository-managed hooks for this clone
 git config core.hooksPath .githooks

# Ensure the hook is executable
chmod +x .githooks/post-commit
```

The `core.hooksPath` setting is local to your clone and is not transferred by Git. Every workshop participant must run the setup command once on their own machine.

Verify the setup:

```bash
git config --get core.hooksPath
ls -l .githooks/post-commit
```

Expected output:

```text
.githooks
```

### 3. Prepare optional AI work metadata

Before an AI-assisted commit, create the local untracked `.ai-worklog.json` file:

```json
{
  "agent": "Developer Agent + create-development-plan",
  "context": "docs/product-brief.md, src/game/win-detector.ts, src/game/win-detector.test.ts",
  "reason": "Implement five-or-more-in-a-row detection for the Caro MVP.",
  "optimization": "Used scoped file context and reused accepted game rules; avoided a repository-wide scan."
}
```

`.ai-worklog.json` is ignored by `.gitignore`. After the commit, the post-commit hook reads it, adds a row to `docs/token-log.md`, and removes the local file.

### 4. Commit and record the unit of work

```bash
git add .
git commit -m "feat: implement win detection"
```

The hook records:

- Commit time.
- Commit message and short SHA.
- Changed files when no worklog metadata is available.
- Agent, context, reason, and optimization when `.ai-worklog.json` is present.

The hook runs after the **local commit**, not after `git push`. Review the generated `docs/token-log.md` entry and include it in the next normal commit before pushing:

```bash
git add docs/token-log.md
git commit -m "docs: record AI usage for previous unit"
git push
```

Do not make the hook create a commit automatically; that can cause recursive commits and confusing history.

### 5. Track AIC separately

The post-commit hook cannot read GitHub Copilot's actual AI Credit or raw token usage. Record the starting and ending AIC values manually in [`docs/token-log.md`](docs/token-log.md) using the GitHub Copilot usage dashboard.

The hook records the unit-of-work evidence; the dashboard provides the workshop AIC totals.

## Mob working

The workshop uses BA, Developer, and Test agents across the SDLC. Store requirements, plans, design decisions, implementation, tests, and usage evidence in the repository.

For each unit of work:

1. Read the product brief and relevant requirements.
2. Confirm scope and acceptance criteria.
3. Create a focused plan.
4. Implement one vertical slice.
5. Add or update tests.
6. Run validation.
7. Review the complete diff.
8. Create `.ai-worklog.json` for AI-assisted work.
9. Commit the completed unit.
10. Review and commit the generated token-log entry.

## Contributing workflow

Do not commit credentials, tokens, cookies, private keys, or other secrets. Before requesting review, ensure the change is in scope, tests have actually run, documentation is current, and the complete diff has been reviewed.
