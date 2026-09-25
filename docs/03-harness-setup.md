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

The post-commit hook cannot read GitHub Copilot's actual AI Credit or raw token usage. Record the starting and ending AIC values manually in [`docs/token-log.md`](docs/token-log.md) using the GitHub Copilot dashboard and workshop notes.

The hook records the unit-of-work evidence; the dashboard provides the workshop AIC totals.
