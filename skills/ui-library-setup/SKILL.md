---
name: ui-library-setup
description: Sets up the marso-ai/ui-lib design system and applies design guidelines to the project. Use when working on UI, components, or design tasks.
allowed-tools: Bash, Read, Write, Glob
---

## Step 1: Ensure ui-lib is available

Check if the folder `submodules/ui-lib` exists in the current project root.

Run: `test -d submodules/ui-lib && echo "exists" || echo "missing"`

- If it **exists**: pull latest changes before proceeding:
  ```
  git -C submodules/ui-lib pull
  ```
  If the pull fails (e.g. network issue or auth error), warn the user but continue to Step 2 with the existing local version.
- If **missing**: detect the available auth method and clone:

  **Detect auth method in order of preference:**

  1. **GitHub CLI** — check with `gh auth status 2>&1 | grep -q "Logged in" && echo "gh" || echo "no"`
     - If logged in: `gh repo clone marso-ai/ui-lib submodules/ui-lib`
     - If `gh` is installed but not logged in: stop and tell the user to run `gh auth login` (it opens a browser — the easiest setup option)
     - If `gh` is not installed: attempt to install it by detecting the OS:
       - **macOS**: `brew install gh`
       - **Linux (apt)**: `sudo apt install gh`
       - **Linux (dnf)**: `sudo dnf install gh`
       - **Windows**: `winget install GitHub.cli`
       - If the OS/package manager can't be determined, show the user the manual install page: https://cli.github.com and ask if they want to skip to SSH/HTTPS fallback
       
       After a successful install, prompt the user to run `gh auth login` (browser-based) before continuing.
       If the install fails or the user chooses to skip, fall through to the next method

  2. **SSH** — check with `ssh -T git@github.com 2>&1 | grep -q "successfully authenticated" && echo "ssh" || echo "no"`
     - If available: `git clone git@github.com:marso-ai/ui-lib.git submodules/ui-lib`
     - If not: fall through to next method

  3. **HTTPS (PAT)** — attempt: `git clone https://github.com/marso-ai/ui-lib.git submodules/ui-lib`
     - If this fails, stop and present the user with all three setup options:
       - **Easiest**: Install GitHub CLI (`brew install gh` or https://cli.github.com) then run `gh auth login`
       - **SSH**: Run `ssh-keygen -t ed25519 -C "your@email.com"` and add `~/.ssh/id_ed25519.pub` to GitHub under *Settings → SSH keys*
       - **PAT**: Generate a token at *github.com → Settings → Developer settings → Personal access tokens*, then run `git config --global credential.helper store`

## Step 2: Sync Design Rules

Copy the design guidelines from the submodule into the project's Claude rules:

```
mkdir -p .claude/rules
cp submodules/ui-lib/CLAUDE.md .claude/rules/m-xr-design.md
```

- If `submodules/ui-lib/CLAUDE.md` does not exist, warn the user and skip this step.
- If `.claude/rules/m-xr-design.md` already exists, overwrite it silently.
- Confirm to the user once the file has been copied.