# Git Setup

One-time setup for git on your machine. This covers SSH keys (especially important if you have a personal GitHub account alongside FLP), global git configuration, and shared hooks.

Project-specific setup (pre-commit config, repo hooks, etc.) lives in each repo's own docs.

## SSH Keys (Multi-Account Setup)

If you use GitHub for both personal projects and FLP, SSH keys with host aliases are the cleanest way to keep accounts separate. Each account gets its own key, and git automatically uses the right one based on the remote URL.

This approach avoids the credential confusion that comes with HTTPS/PAT-based setups, where tokens for one account can interfere with the other.

### Generate two keys

One for your personal GitHub account and one for FLP:

```bash
# Personal key
ssh-keygen -t ed25519 -C "personal@example.com" -f ~/.ssh/id_ed25519

# FLP key
ssh-keygen -t ed25519 -C "you@free.law" -f ~/.ssh/id_ed25519_flp
```

Use a strong passphrase for each key when prompted.

### Start the SSH agent and add both keys

**macOS:**

```bash
eval "$(ssh-agent -s)"
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
ssh-add --apple-use-keychain ~/.ssh/id_ed25519_flp
```

**Linux:**

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
ssh-add ~/.ssh/id_ed25519_flp
```

**Windows (PowerShell as Admin):**

```powershell
Get-Service ssh-agent | Set-Service -StartupType Automatic
Start-Service ssh-agent

ssh-add ~\.ssh\id_ed25519
ssh-add ~\.ssh\id_ed25519_flp
```

### Add public keys to GitHub

Copy each public key and add it to the corresponding GitHub account under **Settings → SSH and GPG keys → New SSH key**.

**macOS:**

```bash
pbcopy < ~/.ssh/id_ed25519.pub      # personal → personal GitHub
pbcopy < ~/.ssh/id_ed25519_flp.pub  # FLP → FLP GitHub
```

**Linux:**

```bash
cat ~/.ssh/id_ed25519.pub      # copy the output manually
cat ~/.ssh/id_ed25519_flp.pub
```

**Windows (PowerShell):**

```powershell
Get-Content ~\.ssh\id_ed25519.pub | Set-Clipboard
Get-Content ~\.ssh\id_ed25519_flp.pub | Set-Clipboard
```

### Configure SSH host aliases

Create or edit `~/.ssh/config` so that each GitHub account uses its own key. The trick is a host alias — `github.com-flp` still connects to `github.com`, but uses your FLP key:

**macOS:**

```
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
    AddKeysToAgent yes
    UseKeychain yes

Host github.com-flp
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_flp
    AddKeysToAgent yes
    UseKeychain yes
```

**Linux / Windows:**

```
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
    AddKeysToAgent yes

Host github.com-flp
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_flp
    AddKeysToAgent yes
```

> `UseKeychain yes` is macOS-only. Linux and Windows don't need it.

### Test the connection

```bash
ssh -T git@github.com       # should greet your personal username
ssh -T git@github.com-flp   # should greet your FLP username
```

### Clone FLP repos with the host alias

Use `github.com-flp` instead of `github.com` in clone URLs:

```bash
git clone git@github.com-flp:freelawproject/courtlistener.git
```

For existing clones, update the remote:

```bash
git remote set-url origin git@github.com-flp:freelawproject/courtlistener.git
```

> **Single GitHub account?** If you only have one GitHub account, you can skip the host alias setup. Use a single key and the standard `github.com` host for everything.

## Global Gitconfig

### Identity and conditional includes

Set your default (personal) identity in `~/.gitconfig`, then use `includeIf` to automatically switch to your FLP email for repos in your FLP directory:

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

This way, FLP repos get your FLP email and personal repos get your personal email, automatically. No need to remember `git config user.email` per repo.

### Recommended settings

These are worth adding to your `~/.gitconfig`:

| Setting                       | What it does                              |
| ----------------------------- | ----------------------------------------- |
| `push.autoSetupRemote = true` | Just `git push` — no `-u origin branch`   |
| `pull.ff = only`              | Prevents accidental merge commits on pull |
| `fetch.prune = true`          | Auto-removes stale remote branch refs     |
| `rerere.enabled = true`       | Remembers how you resolved conflicts      |
| `merge.conflictstyle = diff3` | Shows common ancestor in conflicts        |
| `init.defaultBranch = main`   | Default branch name                       |
| `core.autocrlf = input`       | Normalizes line endings to LF             |

```gitconfig
# Add to ~/.gitconfig
[push]
    autoSetupRemote = true
[pull]
    ff = only
[fetch]
    prune = true
[rerere]
    enabled = true
[merge]
    conflictstyle = diff3
[init]
    defaultBranch = main
[core]
    autocrlf = input
```

### Useful aliases

```gitconfig
# Add to ~/.gitconfig under [alias]
[alias]
    st = status -sb
    co = checkout
    br = branch
    lg = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
    undo = reset HEAD~1 --mixed
    unstage = reset HEAD --
    amend = commit --amend --no-edit
    tidyup = "!git branch --merged | grep -v '\\*\\|main\\|master' | xargs -n 1 git branch -d"
```

| Alias     | What it does                           |
| --------- | -------------------------------------- |
| `st`      | Short status with branch info          |
| `co`      | Checkout                               |
| `br`      | Branch                                 |
| `lg`      | Visual branch history graph            |
| `undo`    | Undo last commit, keep changes staged  |
| `unstage` | Remove files from staging              |
| `amend`   | Add staged changes to last commit      |
| `tidyup`  | Delete local branches merged on remote |

## Global Gitignore

Set up a global gitignore for files that should never be committed from any repo:

```bash
git config --global core.excludesfile ~/.gitignore
```

A good starting point for `~/.gitignore`:

```gitignore
# OS files
.DS_Store
Thumbs.db
*~

# Editor/IDE files
.idea/
*.swp
*.swo
.vscode/settings.json
.vscode/launch.json

# Secrets
.env
.env.*
!.env.example
*.pem
*.key
credentials.json
```

Project-specific patterns (like `node_modules/`, `dist/`, or `__pycache__/`) belong in each repo's own `.gitignore`, not here.

## Commit Message Template

Set up a template that reminds you of the conventional commit format every time you run `git commit` (without `-m`):

```bash
git config --global commit.template ~/.gitmessage
```

Create `~/.gitmessage` with:

```
# <type>(<scope>): <subject>
# |<----  Using a Maximum Of 50 Characters  ---->|

# Explain why this change is being made
# |<----   Try To Limit Each Line to a Maximum Of 72 Characters   ---->|

# Provide links or keys to any relevant tickets, articles or other resources
# Example: Fixes: #23

# --- COMMIT END ---
# Type can be: feat, fix, docs, style, refactor, perf, test, build, ci, chore, revert
# Remember to:
#   - Use the imperative mood in the subject line
#   - Do not end the subject line with a period
#   - Separate subject from body with a blank line
#   - Use the body to explain what and why vs. how
```

See [Conventional Commits](https://www.conventionalcommits.org/) for the full guide on commit types and examples.

## Global Hooks

FLP uses a shared `commit-msg` hook that validates conventional commit format and strips AI-generated taglines from commit messages. The [claude-starter](https://github.com/freelawproject/claude-starter) repo has the hook and installation instructions.

Once installed, set the global hooks path:

```bash
git config --global core.hooksPath ~/.git-hooks
```

### How global hooks work with repo hooks

Global hooks (`core.hooksPath`) and repo-level hooks serve different purposes:

- **Global hooks** (`~/.git-hooks/`) — Run for every repo. Our `commit-msg` hook lives here.
- **Repo hooks** (`pre-commit` tool) — Run per-repo linters and formatters. Installed via `pre-commit install` in each repo.

Both run on commit. The repo-level `pre-commit` hook runs first (formatting, linting), then the global `commit-msg` hook validates your message. Each repo's own setup docs cover installing its `pre-commit` hooks — see [Day One](day-one.md#6-clone-your-repo-and-bootstrap).
