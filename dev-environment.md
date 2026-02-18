# Dev Environment

This page covers general tooling setup. Project-specific setup (Docker, databases, etc.) lives in each repo's own docs.

## macOS Basics

Install [Homebrew](https://brew.sh/) if you don't have it:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Recommended terminal: [iTerm2](https://iterm2.com/) with [oh-my-zsh](https://ohmyz.sh/).

## Git Configuration

### FLP Email

Use `includeIf` in your `~/.gitconfig` to automatically set your FLP email for repos in your FLP directory:

```gitconfig
# ~/.gitconfig
[user]
    name = Your Name
    email = personal@example.com

[includeIf "gitdir:~/flp/"]
    path = ~/.gitconfig-flp
```

```gitconfig
# ~/.gitconfig-flp
[user]
    email = you@free.law
```

This way, FLP repos get your FLP email and personal repos get your personal email, automatically.

### Global Hooks

FLP uses a shared hooks directory at `~/.git-hooks/`. Set it as your global hooks path:

```bash
git config --global core.hooksPath ~/.git-hooks/
```

See [Git Workflow](git-workflow.md) for details on pre-commit hooks.

## Python

### Virtual Environments

Use `venv` for Python projects:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

> **Tip:** Each FLP Python repo has its own requirements files. Activate the venv before installing.

### Linting and Formatting

We use [ruff](https://docs.astral.sh/ruff/) for both linting and formatting, configured via [pre-commit](https://pre-commit.com/):

```bash
pip install pre-commit
pre-commit install
```

Pre-commit runs automatically on `git commit`. See [Coding Standards](coding-standards.md) for style details.

## Node.js

Use [fnm](https://github.com/Schniz/fnm) (Fast Node Manager) to manage Node versions:

```bash
brew install fnm
```

Add to your shell config (`~/.zshrc`):

```bash
eval "$(fnm env --use-on-cd)"
```

Then for any repo with a `.node-version` or `.nvmrc` file, fnm switches automatically.

## Docker

Several FLP projects (notably CourtListener) run in Docker. Install [Docker Desktop](https://www.docker.com/products/docker-desktop/) or use Homebrew:

```bash
brew install --cask docker
```

Project-specific Docker instructions live in each repo's README or wiki.

## Editor Setup

### VS Code (Recommended)

Recommended extensions:

- **Python** (`ms-python.python`) - Python language support
- **Ruff** (`charliermarsh.ruff`) - Linting and formatting
- **Django** (`batisteo.vscode-django`) - Template syntax highlighting
- **GitLens** (`eamodio.gitlens`) - Git blame and history
- **EditorConfig** (`editorconfig.editorconfig`) - Consistent formatting

### Settings

Enable format-on-save with ruff:

```json
{
  "[python]": {
    "editor.defaultFormatter": "charliermarsh.ruff",
    "editor.formatOnSave": true
  }
}
```

## Claude Code

See the dedicated [AI-Assisted Development](ai-assisted-dev.md) page for Claude Code installation, configuration, and usage conventions.
