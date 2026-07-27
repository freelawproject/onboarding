# AI-Assisted Development

FLP has a team license for [Claude Code](https://docs.anthropic.com/en/docs/claude-code), Anthropic's CLI tool for AI-assisted development.

> **AI Use Policy:** A formal AI use policy is being developed. In the meantime, use good judgment: review AI-generated code carefully, don't share sensitive data in prompts, and treat AI output the same as any other code you write — you're responsible for it.

## Getting Access

Ask your manager or team lead to add you to the FLP Claude Code team. Once added, you can authenticate via:

```bash
claude auth login
```

## claude-starter

The [claude-starter](https://github.com/freelawproject/claude-starter) repo contains FLP's recommended Claude Code configuration:

- **settings.json** — Permission rules (allow read-only commands, deny destructive ones)
- **commit-msg githook** — Validates conventional commit format
- **/commit and /review-pr skills** — Custom slash commands for common workflows
- **Daily shortcuts reference** — Quick reference for built-in and custom commands
- **gh/glab CLI setup** — Secure authentication for GitHub and GitLab CLIs

Start there to get a solid baseline configuration, then customize to your preferences.

## Installation and Setup

1. Install Claude Code following the [official docs](https://docs.anthropic.com/en/docs/claude-code)
2. Clone [claude-starter](https://github.com/freelawproject/claude-starter) and review its README
3. Copy the recommended `settings.json` to `~/.claude/settings.json`
4. Install the githooks and skills as described in the claude-starter README

## Key Shortcuts

| Shortcut                       | What it does                                  |
| ------------------------------ | --------------------------------------------- |
| **Shift + Tab**                | Toggle modes — Normal, Planning, Accept Edits |
| **ESC + ESC**                  | Interrupt / bail out                          |
| **`/help`**                    | List available commands                       |
| **`/init`**                    | Create a project CLAUDE.md                    |
| **`/clear`**                   | Clear conversation context                    |
| **`/compact`**                 | Compress conversation to save context         |
| **`claude --continue`** (`-c`) | Resume your last session                      |
| **`claude --resume`** (`-r`)   | Pick from recent sessions                     |

## CLAUDE.md Conventions

Claude Code reads `CLAUDE.md` files for project context and instructions. They layer:

| Level       | Location                   | Purpose                                              |
| ----------- | -------------------------- | ---------------------------------------------------- |
| **Global**  | `~/.claude/CLAUDE.md`      | Personal preferences, universal tooling              |
| **Project** | `<repo>/CLAUDE.md`         | Project-specific conventions, architecture, commands |
| **Local**   | `<repo>/.claude/CLAUDE.md` | Team-shared skills and overrides (committed to repo) |

### What Goes Where

- **Global**: Your editor preferences, commit conventions, shell setup
- **Project**: Repo-specific build commands, architecture notes, testing instructions
- **Local `.claude/`**: Only for team-shared skills that should be committed

Project-specific instructions belong in the repo's root `CLAUDE.md`, not in `.claude/`. Use `/init` to create one.

## Using AI in the Dev Flow

### Exploring Code

Claude Code is great for understanding unfamiliar codebases:

- "What does this function do?"
- "How does authentication work in this project?"
- "Find all the places where we handle pagination"

### Writing Implementations

Use planning mode (Shift + Tab) for non-trivial tasks. This lets Claude explore the codebase and propose an approach before writing code.

### Reviewing PRs

Use the `/review-pr` skill (from claude-starter) to get an AI review of a pull request. This complements but doesn't replace human review.

## Trust but Verify

AI makes mistakes. Always:

- **Review generated code** before committing — read every line
- **Run the tests** — don't assume AI code passes
- **Have Claude review itself** — ask it to check its own work for bugs or edge cases
- **Understand what it wrote** — if you can't explain the code, don't ship it

## Default Permissions Philosophy

The recommended settings.json follows a simple pattern:

- **Reading and searching**: allowed automatically (git log, git diff, file reads)
- **Writing**: asks for permission first (file edits, git commits)
- **Destructive actions**: denied (git push, force operations, deleting things)

This gives Claude enough access to be useful while keeping you in control of anything that changes state.
