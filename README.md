# git-github-complete-guide
A complete practical guide to Git and GitHub: first commit, branching, merging, pull requests, conflict resolution, GitHub Actions CI/CD, team workflows, and fixing common mistakes.
# Git and GitHub — The Complete Practical Guide

> A hands-on guide to Git version control and GitHub collaboration: from your first commit to advanced branching strategies, pull requests, CI/CD, and real-world team workflows.

---

## Table of Contents

1. [What Is Git and Why Does It Matter?](#what-is-git-and-why-does-it-matter)
2. [Installing and Configuring Git](#installing-and-configuring-git)
3. [Core Concepts — How Git Thinks](#core-concepts--how-git-thinks)
4. [Your First Repository](#your-first-repository)
5. [The Daily Git Workflow](#the-daily-git-workflow)
6. [Branching — Working in Parallel](#branching--working-in-parallel)
7. [Merging and Rebasing](#merging-and-rebasing)
8. [GitHub — Collaboration at Scale](#github--collaboration-at-scale)
9. [Pull Requests — The Heart of Collaboration](#pull-requests--the-heart-of-collaboration)
10. [Resolving Merge Conflicts](#resolving-merge-conflicts)
11. [Git History — Viewing, Searching, Undoing](#git-history--viewing-searching-undoing)
12. [Advanced Git Techniques](#advanced-git-techniques)
13. [GitHub Actions — Automating Your Workflow](#github-actions--automating-your-workflow)
14. [Real-World Team Workflows](#real-world-team-workflows)
15. [Common Mistakes and How to Fix Them](#common-mistakes-and-how-to-fix-them)
16. [Quick Reference](#quick-reference)

---

## What Is Git and Why Does It Matter?

**Git** is a distributed version control system — software that tracks every change made to your code over time, allowing you to:

- Go back to any previous version of your code
- Work on new features without breaking the working version
- Collaborate with other developers without overwriting each other's work
- See who changed what, when, and why

**GitHub** is a platform built on top of Git that adds:
- Remote storage for your repositories (backup + sharing)
- Collaboration tools (pull requests, code review, issues)
- Project management (boards, milestones)
- Automation (GitHub Actions CI/CD)
- Community (open source, portfolio)

### Git vs No Git

```
Without Git:
  project_v1/
  project_v2/
  project_v2_final/
  project_v2_final_REAL/
  project_v2_final_REAL_USE_THIS/
  
With Git:
  project/
  (full history of every change, internally)
  (can restore any past state instantly)
  (can see exactly what changed between any two versions)
```

Every professional developer uses Git daily. It is non-negotiable in the industry.

---

## Installing and Configuring Git

### Installation

```bash
# macOS (via Homebrew)
brew install git

# Ubuntu / Debian
sudo apt update && sudo apt install git

# Windows
# Download from: https://git-scm.com/download/win
# Or use: winget install --id Git.Git
```

### First-Time Configuration

```bash
# Set your identity (appears in every commit you make)
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Set default branch name to 'main' (modern standard)
git config --global init.defaultBranch main

# Set your preferred editor for commit messages
git config --global core.editor "code --wait"    # VS Code
git config --global core.editor "nano"           # Nano (simple)
git config --global core.editor "vim"            # Vim

# Enable helpful color output
git config --global color.ui auto

# Verify your configuration
git config --list
```

### SSH Key Setup (Recommended for GitHub)

```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "you@example.com"
# Press Enter for default location
# Set a passphrase (recommended)

# Start SSH agent
eval "$(ssh-agent -s)"

# Add key to agent
ssh-add ~/.ssh/id_ed25519

# Copy public key to clipboard
cat ~/.ssh/id_ed25519.pub
# Then: GitHub → Settings → SSH Keys → New SSH Key → Paste

# Test connection
ssh -T git@github.com
# Should say: "Hi username! You've successfully authenticated..."
```

---

## Core Concepts — How Git Thinks

Understanding Git's mental model prevents confusion and makes everything click.

### The Three Areas

```
Working Directory    Staging Area (Index)    Repository (.git)
─────────────────    ────────────────────    ─────────────────
Your files as        Files you've            Committed snapshots
you edit them        prepared to commit      (permanent history)

     │                      │                       │
     │──── git add ────────▶│                       │
     │                      │──── git commit ──────▶│
     │◀─── git checkout ────────────────────────────│
     │◀─── git restore ──────│                      │
```

### The Four States of a File

```
Untracked    → Git doesn't know this file exists yet
Tracked      → Git is watching this file
  Modified   → File has changed since last commit
  Staged     → Change is prepared for the next commit
  Committed  → Change is saved in repository history
```

### What a Commit Really Is

A commit is not a diff — it's a **snapshot** of your entire project at a point in time, plus:
- A unique SHA-1 hash (e.g., `a3f8c2d`)
- Author name and email
- Timestamp
- Commit message
- Reference to parent commit(s)

```
Commit history:
  a3f8c2d ← HEAD (current position)
      │
  7b91e4a
      │
  2d45f18
      │
  (initial commit)
```

---

## Your First Repository

### Starting a New Project

```bash
# Create directory and initialize Git
mkdir my-project
cd my-project
git init

# You'll see:
# Initialized empty Git repository in /path/to/my-project/.git/

# Check status
git status
# On branch main
# No commits yet
# nothing to commit
```

### Creating Your First Commit

```bash
# Create a file
echo "# My Project" > README.md

# Check status
git status
# Untracked files:
#   README.md

# Stage the file
git add README.md

# Or stage everything:
git add .

# Check status again
git status
# Changes to be committed:
#   new file: README.md

# Commit with a message
git commit -m "Initial commit: add README"

# View the commit
git log
# commit a3f8c2d1234...
# Author: Your Name <you@example.com>
# Date:   Mon Jun 27 2025
#
#     Initial commit: add README
```

### Connecting to GitHub

```bash
# Create repository on GitHub first (github.com/new)
# Then connect your local repo:

git remote add origin git@github.com:username/my-project.git

# Push your commits to GitHub
git push -u origin main
# -u sets 'origin main' as the default upstream
# Future pushes: just 'git push'

# Verify remote
git remote -v
# origin  git@github.com:username/my-project.git (fetch)
# origin  git@github.com:username/my-project.git (push)
```

### Cloning an Existing Repository

```bash
# Clone from GitHub (creates a local copy)
git clone git@github.com:username/repo-name.git

# Clone into a specific folder
git clone git@github.com:username/repo-name.git my-folder

# Clone via HTTPS (if no SSH key)
git clone https://github.com/username/repo-name.git
```

---

## The Daily Git Workflow

This is the cycle you'll repeat dozens of times per day:

```bash
# 1. Pull latest changes from team
git pull

# 2. Make changes to your files
# ... edit code ...

# 3. See what changed
git status
git diff                  # What changed in unstaged files
git diff --staged         # What's staged for commit

# 4. Stage specific files
git add src/app.py
git add tests/

# Or stage all changes
git add .

# 5. Commit with a meaningful message
git commit -m "Add user authentication endpoint"

# 6. Push to GitHub
git push
```

### Writing Good Commit Messages

```bash
# BAD commit messages (useless history):
git commit -m "fix"
git commit -m "changes"
git commit -m "asdf"
git commit -m "WIP"

# GOOD commit messages (useful history):
git commit -m "Fix null pointer exception in user login"
git commit -m "Add rate limiting to API endpoints"
git commit -m "Refactor database connection pooling"
git commit -m "Update dependencies to resolve security vulnerability"

# Conventional Commits format (widely used):
# <type>(<scope>): <description>
git commit -m "feat(auth): add OAuth2 Google login"
git commit -m "fix(api): handle empty response from payment gateway"
git commit -m "docs(readme): update installation instructions"
git commit -m "refactor(db): extract query builder to separate module"
git commit -m "test(auth): add unit tests for JWT validation"
git commit -m "chore(deps): upgrade React to 18.3.0"

# Types: feat, fix, docs, style, refactor, test, chore, perf, ci
```

### The .gitignore File

```bash
# .gitignore tells Git which files to never track

# Common .gitignore for Python project:
cat > .gitignore << 'EOF'
# Python
__pycache__/
*.py[cod]
*.egg-info/
dist/
build/
.eggs/
venv/
.env

# IDE
.vscode/
.idea/
*.swp

# OS
.DS_Store
Thumbs.db

# Secrets (CRITICAL)
*.env
.env.local
.env.production
secrets.json
*_credentials.json
EOF

# Common additions for Node.js:
echo "node_modules/" >> .gitignore
echo ".next/" >> .gitignore
echo "dist/" >> .gitignore

# GitHub provides .gitignore templates for every language
# github.com/github/gitignore
```

---

## Branching — Working in Parallel

Branches allow you to work on features or fixes without affecting the main codebase.

### Branch Basics

```bash
# List all branches
git branch          # Local branches
git branch -r       # Remote branches
git branch -a       # All branches

# Create a new branch
git branch feature/user-login

# Switch to a branch
git checkout feature/user-login

# Create AND switch (most common)
git checkout -b feature/user-login

# Modern syntax (Git 2.23+)
git switch -c feature/user-login

# Push new branch to GitHub
git push -u origin feature/user-login

# Delete a branch (after merging)
git branch -d feature/user-login          # Local
git push origin --delete feature/user-login  # Remote
```

### Branch Naming Conventions

```bash
# Feature branches
feature/user-authentication
feature/payment-integration
feat/dark-mode

# Bug fixes
fix/login-redirect-loop
bugfix/null-pointer-exception
hotfix/critical-security-patch

# Other
docs/update-api-documentation
refactor/database-layer
chore/upgrade-dependencies
release/v2.1.0
```

### Visualizing Branches

```
main:    A──B──C──────────────M
                \            /
feature:         D──E──F──G
                
A = Initial commit
B = Second commit  
C = Third commit (branch point)
D-G = Feature work
M = Merge commit
```

```bash
# See branch graph in terminal
git log --oneline --graph --all

# Output:
# *   a3f8c2d (HEAD -> main) Merge pull request #4
# |\
# | * 7b91e4a (feature/login) Add password hashing
# | * 2d45f18 Add login endpoint
# |/
# * 9c23a1b Add user model
# * 1a45b2c Initial commit
```

---

## Merging and Rebasing

Two ways to integrate changes from one branch into another.

### Merging

```bash
# Switch to the branch you want to merge INTO
git checkout main

# Merge the feature branch
git merge feature/user-login

# Fast-forward merge (no conflicts, linear history):
# Before: main: A──B──C
#         feature:      D──E
# After:  main: A──B──C──D──E

# Three-way merge (creates a merge commit):
# Before: main:    A──B──C──F
#         feature:      D──E
# After:  main:    A──B──C──F──M (M = merge commit)
#                      D──E──/
```

### Rebasing

```bash
# Rebase: replay your commits on top of another branch
# Creates a linear history

git checkout feature/user-login
git rebase main

# Before:
# main:    A──B──C──F
# feature:      D──E

# After rebase:
# main:    A──B──C──F
# feature:           D'──E' (commits replayed on top of F)

# Then fast-forward merge:
git checkout main
git merge feature/user-login
# main: A──B──C──F──D'──E' (clean linear history)
```

### Merge vs Rebase — When to Use Which

```
USE MERGE when:
  - Merging a feature branch into main (preserves history)
  - Working on a shared branch (don't rebase shared history)
  - You want to see when branches were integrated

USE REBASE when:
  - Updating your feature branch with latest main changes
  - Cleaning up local commits before a pull request
  - You want a linear, readable history

GOLDEN RULE: Never rebase commits that have been pushed
             to a shared remote branch. It rewrites history
             and creates confusion for teammates.
```

### Interactive Rebase — Cleaning Up History

```bash
# Rewrite the last 3 commits (before pushing)
git rebase -i HEAD~3

# Editor opens with:
# pick a3f8c2d Add login endpoint
# pick 7b91e4a Fix typo in login
# pick 2d45f18 Fix another typo

# Change 'pick' to:
# squash (s) = combine with previous commit
# reword (r) = change commit message
# drop (d)   = delete this commit
# fixup (f)  = combine, discard this message

# Result: 3 messy commits → 1 clean commit
# pick a3f8c2d Add login endpoint
# squash 7b91e4a Fix typo in login
# squash 2d45f18 Fix another typo
```

---

## GitHub — Collaboration at Scale

### Forking vs Cloning

```
CLONE: Copy a repository to your local machine
       (you need write access to push back)

FORK:  Copy someone else's repository to YOUR GitHub account
       (standard for contributing to open source)
       
Open source contribution workflow:
1. Fork the repository (your copy on GitHub)
2. Clone YOUR fork locally
3. Make changes on a branch
4. Push to YOUR fork
5. Open a Pull Request to the original repository
```

### Issues — Tracking Work

```markdown
# Good issue titles:
"Bug: Login fails with special characters in password"
"Feature: Add dark mode toggle to settings"
"Docs: API authentication examples are outdated"

# Issue body template:
## Description
Clear description of the bug or feature request.

## Steps to Reproduce (for bugs)
1. Go to login page
2. Enter password with '@' character
3. Click Login
4. See error

## Expected Behavior
User should be logged in successfully.

## Actual Behavior
Error: "Invalid credentials" despite correct password.

## Environment
- OS: macOS 14.5
- Browser: Chrome 125
- App version: 2.3.1
```

### Linking Issues to Commits and PRs

```bash
# Close an issue automatically when PR is merged:
git commit -m "Fix login with special characters

Closes #42"

# Keywords: Closes, Fixes, Resolves
# Closes #42 — issue 42 closes when PR merges
```

---

## Pull Requests — The Heart of Collaboration

A Pull Request (PR) is a proposal to merge your branch into another branch. It's the primary mechanism for code review.

### Creating a Good Pull Request

```markdown
# PR Title:
"Add OAuth2 Google authentication"

# PR Description template:
## What does this PR do?
Adds Google OAuth2 login as an alternative to email/password authentication.
Users can now click "Sign in with Google" on the login page.

## Why?
Addresses user feedback requesting social login options (#38).
Reduces friction for new user registration.

## How to test
1. Run `npm install` (new dependencies added)
2. Add GOOGLE_CLIENT_ID and GOOGLE_CLIENT_SECRET to .env
3. Start the server: `npm run dev`
4. Visit /login and click "Sign in with Google"
5. Complete OAuth flow
6. Verify you're redirected to dashboard

## Screenshots
[Attach screenshots of the new login button and OAuth flow]

## Checklist
- [x] Tests added/updated
- [x] Documentation updated
- [x] No console.log statements left
- [x] .env.example updated with new variables
- [ ] Needs design review

## Related Issues
Closes #38
```

### Code Review Best Practices

```
AS A REVIEWER:
  ✅ Review within 24 hours
  ✅ Be specific: "Line 42: consider using Optional here instead of null check"
  ✅ Explain why: "This could cause a race condition because..."
  ✅ Ask questions: "What happens if the API returns 429?"
  ✅ Approve explicitly when satisfied
  ❌ Don't just say "looks good" without reading
  ❌ Don't block on style preferences (that's what linters are for)
  ❌ Don't rewrite the author's code in comments

AS AN AUTHOR:
  ✅ Keep PRs small (<400 lines changed when possible)
  ✅ Respond to every comment
  ✅ Explain your decisions when pushed back on
  ✅ Request re-review after addressing comments
  ❌ Don't take code review personally
  ❌ Don't merge without approvals
```

### PR Review Commands

```bash
# Fetch and checkout a PR locally (to test it)
git fetch origin pull/42/head:pr-42
git checkout pr-42

# Or with GitHub CLI
gh pr checkout 42
gh pr review 42 --approve
gh pr review 42 --request-changes --body "Please add tests for edge cases"
```

---

## Resolving Merge Conflicts

Merge conflicts happen when two branches modify the same part of the same file.

### Understanding Conflict Markers

```python
# When Git can't auto-merge, it marks conflicts like this:

def calculate_price(item, quantity):
<<<<<<< HEAD
    # Your version (current branch)
    discount = 0.1 if quantity > 10 else 0
    return item.price * quantity * (1 - discount)
=======
    # Their version (incoming branch)
    bulk_discount = quantity // 10 * 0.05
    return item.price * quantity * (1 - bulk_discount)
>>>>>>> feature/pricing-update

# <<<<<<< HEAD = start of YOUR changes
# ======= = divider between the two versions
# >>>>>>> = end of THEIR changes
```

### Resolving Conflicts Step by Step

```bash
# Step 1: Pull and see conflict
git pull
# CONFLICT (content): Merge conflict in src/pricing.py

# Step 2: See which files conflict
git status
# both modified: src/pricing.py

# Step 3: Open the file and edit
# Keep your version, their version, or a combination
# Remove the conflict markers (<<<, ===, >>>)

# Final resolved version:
def calculate_price(item, quantity):
    # Combine both approaches
    base_discount = 0.1 if quantity > 10 else 0
    bulk_discount = quantity // 10 * 0.05
    total_discount = max(base_discount, bulk_discount)
    return item.price * quantity * (1 - total_discount)

# Step 4: Stage the resolved file
git add src/pricing.py

# Step 5: Complete the merge
git commit
# (Editor opens with merge commit message — save and close)

# Step 6: Push
git push
```

### Using VS Code for Conflict Resolution

VS Code shows conflicts visually with buttons:
- **Accept Current Change** (your version)
- **Accept Incoming Change** (their version)
- **Accept Both Changes** (keep both)
- **Compare Changes** (side-by-side view)

This is often faster than editing conflict markers manually.

---

## Git History — Viewing, Searching, Undoing

### Viewing History

```bash
# Basic log
git log

# Compact one-line format
git log --oneline

# With graph
git log --oneline --graph --all

# Last N commits
git log -5

# Commits by author
git log --author="Alice"

# Commits in date range
git log --after="2025-01-01" --before="2025-06-01"

# Search commit messages
git log --grep="authentication"

# See changes in each commit
git log -p

# See which files changed
git log --stat

# Show a specific commit
git show a3f8c2d
```

### Searching Code History

```bash
# Find when a specific string was added/removed
git log -S "password_hash" --oneline

# Find commits that changed a function
git log -L :calculate_price:src/pricing.py

# Who last modified each line of a file
git blame src/pricing.py
git blame -L 10,20 src/pricing.py   # Only lines 10-20
```

### Undoing Changes

```bash
# ─────────────────────────────────────────────────────
# UNDO UNSTAGED CHANGES (not yet added)
# ─────────────────────────────────────────────────────
git restore src/file.py           # Discard changes to one file
git restore .                     # Discard ALL unstaged changes
# ⚠️ Cannot be recovered — use carefully

# ─────────────────────────────────────────────────────
# UNDO STAGED CHANGES (added but not committed)
# ─────────────────────────────────────────────────────
git restore --staged src/file.py  # Unstage one file
git restore --staged .            # Unstage everything

# ─────────────────────────────────────────────────────
# UNDO THE LAST COMMIT (not yet pushed)
# ─────────────────────────────────────────────────────
git reset --soft HEAD~1   # Undo commit, keep changes staged
git reset --mixed HEAD~1  # Undo commit, keep changes unstaged (default)
git reset --hard HEAD~1   # Undo commit AND discard all changes ⚠️

# ─────────────────────────────────────────────────────
# FIX THE LAST COMMIT (not yet pushed)
# ─────────────────────────────────────────────────────
git commit --amend -m "New, corrected commit message"
# Or add a forgotten file:
git add forgotten-file.py
git commit --amend --no-edit   # Keep same message

# ─────────────────────────────────────────────────────
# UNDO A PUSHED COMMIT (creates new "undo" commit)
# ─────────────────────────────────────────────────────
git revert a3f8c2d    # Safe — doesn't rewrite history
git push
```

---

## Advanced Git Techniques

### Git Stash — Temporarily Save Work

```bash
# You're mid-feature and need to switch branches urgently
# But you don't want to commit unfinished work

# Save current work to stash
git stash
# or with a description:
git stash push -m "WIP: user authentication form"

# Switch to another branch, do urgent fix, come back
git checkout main
git pull
# ... fix the bug ...
git checkout feature/user-login

# Restore your stashed work
git stash pop          # Apply and remove from stash
git stash apply        # Apply but keep in stash

# List all stashes
git stash list
# stash@{0}: WIP on feature: WIP: user authentication form
# stash@{1}: WIP on main: temporary fix

# Apply a specific stash
git stash apply stash@{1}

# Drop a stash
git stash drop stash@{0}
```

### Git Cherry-Pick — Apply Specific Commits

```bash
# Apply a specific commit from another branch
# Useful for: pulling a hotfix from main into your feature branch

git checkout feature/big-feature
git cherry-pick a3f8c2d   # Apply this specific commit

# Cherry-pick a range of commits
git cherry-pick a3f8c2d..7b91e4a

# Cherry-pick without committing (stage only)
git cherry-pick --no-commit a3f8c2d
```

### Git Tags — Marking Releases

```bash
# Create a lightweight tag
git tag v1.0.0

# Create an annotated tag (recommended for releases)
git tag -a v1.0.0 -m "Release version 1.0.0"

# Tag a specific past commit
git tag -a v0.9.0 a3f8c2d -m "Beta release"

# Push tags to GitHub
git push origin v1.0.0         # Push specific tag
git push origin --tags         # Push all tags

# List tags
git tag
git tag -l "v1.*"              # Filter by pattern

# Delete a tag
git tag -d v1.0.0              # Local
git push origin --delete v1.0.0  # Remote
```

### Submodules — Embedding Other Repos

```bash
# Add a repository as a submodule
git submodule add git@github.com:company/shared-lib.git libs/shared

# After cloning a repo with submodules
git clone --recurse-submodules git@github.com:username/repo.git

# Or initialize after cloning
git submodule init
git submodule update

# Update all submodules to latest
git submodule update --remote
```

---

## GitHub Actions — Automating Your Workflow

GitHub Actions automates tasks triggered by repository events.

### Basic CI Pipeline

```yaml
# .github/workflows/ci.yml

name: CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.11'

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        pip install pytest pytest-cov

    - name: Run linter
      run: |
        pip install flake8
        flake8 . --count --select=E9,F63,F7,F82 --show-source

    - name: Run tests
      run: |
        pytest --cov=src tests/ --cov-report=xml

    - name: Upload coverage
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage.xml
```

### Deploy on Push to Main

```yaml
# .github/workflows/deploy.yml

name: Deploy to Production

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    needs: test   # Only deploy if tests pass

    steps:
    - uses: actions/checkout@v4

    - name: Deploy to server
      env:
        SSH_PRIVATE_KEY: ${{ secrets.SSH_PRIVATE_KEY }}
        SERVER_HOST: ${{ secrets.SERVER_HOST }}
      run: |
        echo "$SSH_PRIVATE_KEY" > key.pem
        chmod 600 key.pem
        ssh -i key.pem user@$SERVER_HOST "
          cd /app &&
          git pull &&
          pip install -r requirements.txt &&
          systemctl restart myapp
        "
```

### Automated PR Checks

```yaml
# .github/workflows/pr-checks.yml

name: PR Checks

on:
  pull_request:

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: Run ESLint
      run: |
        npm install
        npm run lint

  type-check:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: TypeScript check
      run: |
        npm install
        npm run type-check

  security:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: Security audit
      run: npm audit --audit-level=high
```

---

## Real-World Team Workflows

### GitHub Flow (Most Common)

```
Simple, works for most teams:

1. main is always deployable
2. Create a branch for every change
3. Open a PR when ready for review
4. Merge after approval + CI passes
5. Deploy immediately after merge

Branch lifecycle:
  main ──────────────────────────────────────────▶
        \                              /
         feature/login ───────────────
```

### Git Flow (For Versioned Releases)

```
More complex, for software with explicit releases:

main      ──────────────────────────────────▶  (production)
              ↑               ↑
hotfix/   ────┘         ──────┘
develop   ────────────────────────────────▶  (integration)
              ↑   ↑   ↑
feature/  ────┘   │   │
feature/      ────┘   │
release/          ────┘
```

### Trunk-Based Development (Large Teams / Google / Facebook)

```
Everyone commits to main (trunk) daily
Feature flags hide incomplete features
No long-lived branches
Requires strong CI/CD and test coverage

main ──────────────────────────────▶
  ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑
  All devs commit here (small, frequent commits)
```

### Branch Protection Rules (GitHub Settings)

```
For production-grade repositories, set these on main:

☑ Require a pull request before merging
  ☑ Require 2 approvals
  ☑ Dismiss stale pull request approvals when new commits are pushed

☑ Require status checks to pass before merging
  ☑ Require branches to be up to date before merging
  ☑ Required checks: CI / test, lint

☑ Require conversation resolution before merging

☑ Do not allow bypassing the above settings

These rules prevent:
- Direct pushes to main
- Merging without code review
- Merging when tests fail
- Merging with unresolved comments
```

---

## Common Mistakes and How to Fix Them

### "I committed to main instead of a branch"

```bash
# Move the last commit to a new branch
git branch feature/my-work    # Create branch at current position
git reset --hard HEAD~1       # Remove commit from main
git checkout feature/my-work  # Switch to the new branch
```

### "I accidentally committed a secret/password"

```bash
# This is serious — assume the secret is compromised
# 1. Rotate the secret IMMEDIATELY (revoke API key, change password)

# 2. Remove from history (if not yet pushed):
git reset --hard HEAD~1

# 3. If already pushed — use git filter-repo:
pip install git-filter-repo
git filter-repo --path secrets.json --invert-paths

# 4. Force push (coordinate with team first):
git push origin --force-with-lease

# 5. GitHub has secret scanning — they may have already alerted you
```

### "I need to undo a merge"

```bash
# If not yet pushed:
git reset --hard ORIG_HEAD

# If already pushed (create a revert):
git revert -m 1 <merge-commit-hash>
git push
```

### "My local branch is behind remote"

```bash
# Update your branch with remote changes
git pull --rebase origin main

# Or if you want a merge commit:
git pull origin main
```

### "I deleted a branch I needed"

```bash
# Find the lost commit hash in reflog
git reflog

# Output:
# a3f8c2d HEAD@{0}: checkout: moving from deleted-branch to main
# 7b91e4a HEAD@{1}: commit: last commit on deleted branch

# Recreate the branch
git branch recovered-branch 7b91e4a
```

### "Git says 'detached HEAD'"

```bash
# You checked out a specific commit, not a branch
# HEAD is not pointing to any branch

# Solution: Create a branch from here
git checkout -b my-new-branch

# Or return to main
git checkout main
```

---

## Quick Reference

### Daily Commands

```bash
git status                    # What's changed?
git add .                     # Stage all changes
git commit -m "message"       # Commit staged changes
git push                      # Push to remote
git pull                      # Get latest from remote
git log --oneline -10         # Last 10 commits
```

### Branch Commands

```bash
git checkout -b feature/name  # Create and switch to branch
git checkout main             # Switch to main
git branch -d feature/name    # Delete local branch
git push -u origin feature    # Push new branch to remote
git branch -a                 # List all branches
```

### Undo Commands

```bash
git restore file.py           # Discard unstaged changes
git restore --staged file.py  # Unstage a file
git reset --soft HEAD~1       # Undo last commit, keep changes
git commit --amend            # Fix last commit
git revert <hash>             # Safe undo of a pushed commit
git stash                     # Save uncommitted work temporarily
git stash pop                 # Restore stashed work
```

### Inspection Commands

```bash
git diff                      # Unstaged changes
git diff --staged             # Staged changes
git log --oneline --graph --all  # Visual branch history
git blame file.py             # Who wrote each line
git show <hash>               # Show a specific commit
git log -S "search term"      # Find when string was added
```

### GitHub CLI (gh)

```bash
gh repo create                # Create new repo
gh pr create                  # Create pull request
gh pr list                    # List open PRs
gh pr checkout 42             # Check out PR #42 locally
gh issue create               # Create new issue
gh issue list                 # List open issues
gh workflow run               # Trigger a GitHub Action
```

---

## Further Reading

- [Pro Git Book](https://git-scm.com/book) — free, comprehensive, official
- [GitHub Skills](https://skills.github.com/) — interactive learning paths
- [Conventional Commits](https://www.conventionalcommits.org/) — commit message standard
- [Oh My Git!](https://ohmygit.org/) — visual Git learning game
- [Learn Git Branching](https://learngitbranching.js.org/) — interactive browser game
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

---

## License

Released under the [MIT License](LICENSE). Free to use, share, and adapt.

---

*Found this useful? Star the repo ⭐ and share it with a developer just getting started with Git.*
