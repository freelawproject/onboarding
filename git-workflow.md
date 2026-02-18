# Git Workflow

## Sprint Flow

We work in sprints. The basic cycle:

1. Issues are assigned and sized before sprint kick-off
2. Board columns: **ToDo** → **In Progress** → **Done**
3. Move your issue to **In Progress** when you start working on it

## Branching

Branch names follow this format:

```
$ISSUE_NUMBER-description-YYYYMMDD
```

Examples:

- `1234-add-login-page-20260215`
- `567-fix-search-pagination-20260218`

The date suffix helps identify stale branches at a glance.

## Conventional Commits

All commits follow the [Conventional Commits](https://www.conventionalcommits.org/) format:

```
type(scope?): description
```

### Valid Types

| Type | Purpose |
|------|---------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, whitespace (no logic change) |
| `refactor` | Code restructuring (no behavior change) |
| `perf` | Performance improvement |
| `test` | Adding or updating tests |
| `build` | Build system or dependencies |
| `ci` | CI/CD configuration |
| `chore` | Maintenance tasks |
| `revert` | Reverting a previous commit |

### Examples

```
feat(search): add date range filter to opinion search
fix(api): handle null docket entries in serializer
docs: update CourtListener setup instructions
refactor(courts): extract jurisdiction lookup into utility
```

The scope is optional but helpful for larger repos.

## Branch Protection

- **No one can push directly to `main`** — all changes go through PRs
- **Force pushes**: use `--force-with-lease` instead of `--force` on feature branches. It prevents overwriting a teammate's work if they pushed to your branch.

```bash
# Safe force push
git push --force-with-lease origin my-branch

# Never do this
git push --force origin my-branch
```

## Push Early and Often

Push your branch to the remote frequently. This:

- Saves your work (your laptop is not a backup)
- Lets teammates see what you're working on
- Makes it easier to ask for help

Don't wait until the code is "perfect" to push.

**Before pushing**, run your local checks (pre-commit, linting, formatting, tests) to minimize CI failures. Fixing things locally is faster than waiting for the pipeline.

## Pull Requests

### Creating a PR

- Follow the repo's PR template
- Reference the issue number (e.g., "Closes #1234")
- Keep PRs focused — one logical change per PR

### Review Process

All PRs require at least one review before merging.

1. Open a PR and **assign** it to your requested reviewer — assignment signals "this is ready for you to look at"
2. Reviewer reviews → leaves feedback or approves → **reassigns to PR owner**
3. PR owner addresses feedback → **reassigns back to reviewer**
4. Repeat until approved

**Heads up on notifications:** When you add a reviewer, paste the PR link directly to them in Slack as a courtesy. We get a lot of email from GitHub and it's easy for notifications to get buried.

Key norms:

- **People close their own comments/change requests** — don't resolve someone else's feedback
- **PR owner merges** when approved
- **Don't manually close issues or move cards to Done** — the PR template's "Closes #..." auto-closes the issue on merge

### Code Review Expectations

- Keep PRs small and focused for faster reviews
- Include screenshots for UI changes
- No force pushes during review (it breaks the diff history)
- Respond to every comment, even if just with a thumbs up
- **Be direct and honest** — clear feedback helps everyone improve. Don't soften a real concern into something that can be ignored.
- **Don't take critical feedback personally** — a comment on your code is not a comment on your character. We're all here to make the code better.

## CI/CD

Every PR triggers a CI pipeline that runs:

- **Linting** (ruff, ESLint, etc.)
- **Formatting** checks
- **Test suite**

All checks must pass before merging. Run these locally first (see [Pre-Commit Hooks](#pre-commit-hooks)) to avoid waiting on CI for things you could have caught on your machine.

## Deploys

> **TODO:** Deploy workflow documentation is coming soon. This will cover the skip-deploy workflow, steps, examples, and `skip-deploy` label usage.

## Pre-Commit Hooks

Pre-commit hooks run automatically on `git commit`. Our hooks auto-fix issues (formatting, trailing whitespace, etc.) and then **abort the commit** so you can review the fixes.

The workflow:

1. `git commit` → hooks run → auto-fixes applied → commit aborts
2. Review the changes (`git diff`)
3. `git add` the fixed files
4. `git commit` again

> **Warning:** Don't use `--no-verify` to skip hooks. The hooks exist to catch real issues. If a hook is broken, fix it or raise it with the team.
