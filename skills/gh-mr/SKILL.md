---
name: gh-mr
description: Use when user asks to create a merge request, pull request, MR, PR, open a PR/MR, submit changes for review, or merge branch into parent
---

# Create Merge Request (MR/PR)

## Overview

Prepare and open a Merge Request to the branch's parent branch. Guards against creating MRs while on main/master (requires a feature branch first).

**Announce at start:** "Using gh-mr to open a merge request."

## Precondition Check

**If currently on main/master:**

```bash
git branch --show-current
```

If on `main` or `master`, you cannot MR directly from the main branch. STOP and use **gh-init-branch** skill:

> "Using gh-init-branch to create a feature branch first."

Do not continue until a non-main branch is active.

## Process

### Step 1: Detect Parent Branch (Target)

```bash
for b in main master develop staging; do
  mb=$(git merge-base HEAD $b 2>/dev/null)
  [ -n "$mb" ] && echo "$b $(git rev-list --count $mb..HEAD)"
done | sort -k2 -n | head -1
```

Pick the branch with fewest commits since branch-off — that is the parent this feature was created from.

**Fallback:** if no long-lived branch matches, ask user to specify the target.

Do NOT ask the user to confirm unless the result looks wrong.

### Step 2: Confirm Current Branch

```bash
git branch --show-current
```

This is the source branch. Confirm it is the branch containing the work to merge.

### Step 3: Push Branch to Remote (if not pushed)

```bash
git push -u origin <branch-name>
```

If already pushed, `git push` (to sync latest commits).

### Step 4: Verify Changes

```bash
git log --oneline <base-branch>..HEAD
```

List commits that will go into the MR. Review:
- No accidental commits (debug, WIP, merge noise)
- Changes are scoped to the intended work
- If multiple unrelated changes exist, flag to user before proceeding

### Step 5: Draft Title and Description

**Title — follow Conventional Commits:**

```
<type>(<scope>): <imperative summary>
```

| Type | Use |
|------|-----|
| `feat` | New feature |
| `fix` | Bug fix |
| `refactor` | Code change without behavior change |
| `perf` | Performance improvement |
| `docs` | Documentation only |
| `chore` | Build, CI, config, dependencies |
| `ci` | CI/CD pipeline changes |
| `test` | Tests only |

**Title rules:**
- English always
- Imperative mood: "add", "fix", "remove" — not "added", "adds"
- ≤50 chars when possible, hard cap 72
- No trailing period
- Scope optional (e.g. `feat(api): add /users/:id/profile`)

**Description — best practices (English):**
- **Summary:** what changed, in 2-3 bullets
- **Why:** problem solved or motivation
- **Test plan:** how it was verified
- **Breaking changes:** if any, call out explicitly
- **Related items:** issues closed, companion PRs

Template:

```markdown
## Summary
- Bullet 1: what changed
- Bullet 2: what changed

## Why
Short explanation of the problem or motivation.

## Test plan
- [ ] What was verified / how to verify

## Notes
Related PRs, issues, or follow-ups.
```

### Step 6: Confirm Title and Draft Status

Show the title and description to the user. Ask:
- Is the title correct?
- Draft or ready for review?

Do not ask the user to confirm the base branch — it was auto-detected in Step 1.

### Step 7: Create MR

```bash
gh pr create \
  --base <base-branch> \
  --head <current-branch> \
  --title "<title>" \
  --body "<description>" \
  [--draft]
```

Include `--draft` if user requested draft status.

### Step 8: Report

Tell user:
- MR URL (from `gh pr create` output)
- Base branch and source branch
- Title used
- Draft or ready status

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Creating MR from main/master | Guard with gh-init-branch first — never skip |
| Title not following conventional commits | Check against the table before creating |
| Non-English title/description | Always English |
| Forgetting to push latest commits | `git push` before creating |
| Multiple unrelated changes in one MR | Flag to user, split if needed |

## Error Handling

- **Branch not pushed:** Push with `-u` before creating
- **`gh` not authenticated:** Run `gh auth login` and tell user
- **Branch already has an open MR:** Report existing MR URL, do not create duplicate
- **No commits to merge:** Inform user branch is up-to-date with base; ask how to proceed
