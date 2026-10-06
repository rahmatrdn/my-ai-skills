---
name: gh-init-branch
description: Use when user asks to start new work, create a feature/fix branch, initialize a branch for development, or when beginning any task that requires a dedicated branch from main/master
---

# Initialize Branch for Development

## Overview

Prepare a clean branch for new work. Handles stash of dirty state, sync with remote, and branch creation with conventional prefix.

**Announce at start:** "Using gh-init-branch to set up a new branch for development."

## Branch Naming Convention

Prefix branch name with type based on user's intent:

| Intent | Prefix | Example |
|--------|--------|---------|
| New feature | `feat/` | `feat/add-user-auth` |
| Bug fix | `fix/` | `fix/cart-total-calculation` |
| Refactor | `fix/refactor-` | `fix/refactor-order-module` |
| Chore (deps, CI, config) | `chore/` | `chore/upgrade-next-14` |
| Documentation | `docs/` | `docs/api-reference` |
| Performance | `perf/` | `perf/optimize-image-loading` |
| Hotfix (urgent production) | `fix/` | `fix/critical-payment-error` |
| Experiment/POC | `feat/` | `feat/poc-graphql-subscriptions` |

**Rules:**
- Lowercase only, hyphens as separators (no spaces, no underscores)
- No trailing hyphens
- Keep names concise but descriptive
- If user specifies a name verbatim, use it as-is after adding prefix

## Process

### Step 1: Detect Current Branch

```bash
git branch --show-current
```

### Step 2a: If NOT on main/master (dirty or clean)

```bash
# Stash any uncommitted changes (only if working tree is dirty)
git stash push -m "WIP: auto-stash before branch init"

# Switch to main/master
git checkout main || git checkout master

# Pull latest from remote
git pull origin main || git pull origin master

# Create and switch to new branch
git checkout -b <branch-name>
```

If stash was created, restore only that specific stash (use its index):

```bash
# Check stash list to find the index of "WIP: auto-stash before branch init"
git stash list

# Pop only that specific stash by index (e.g., stash@{0})
git stash pop stash@{<index>}
```

Notify user:
> "Restored stashed changes on the new branch."

### Step 2b: If already on main/master

```bash
# Pull latest from remote
git pull origin main || git pull origin master

# Create and switch to new branch
git checkout -b <branch-name>
```

### Step 3: Verify

```bash
git branch --show-current
git log --oneline -1
```

Confirm:
- Current branch is the newly created one
- Latest commit is up-to-date with remote

### Step 4: Report

Tell user:
- Branch name created
- Base branch used (main or master)
- Whether changes were stashed
- Ready to start work

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Forgetting to pull before creating branch | Always `git pull` on main/master before `checkout -b` |
| Stashing then forgetting about it | Notify user about stash; suggest `git stash pop` when ready |
| Branch name too vague (`fix/fix-thing`) | Ask user for clarification, suggest descriptive name |
| Creating branch on main without pulling | Always pull first to avoid stale base |

## Error Handling

- **Remote not configured:** Ask user to confirm remote name (default: `origin`)
- **Branch already exists:** Suggest alternative name or ask user which to use
- **Stash conflicts after pop:** Advise manual resolution, do not force
- **Detached HEAD state:** Switch to main/master first, then create branch
