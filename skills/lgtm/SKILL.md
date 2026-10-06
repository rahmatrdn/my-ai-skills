---
name: lgtm
description: Create a new Git branch from `main`, commit the current changes, push the branch, and create a new pull request.
user-invocable: true
---
# Git Branch PR

Create a new Git branch from `main`, commit the current changes, push the branch, and create a new pull request.

## When to Use

Use this skill when the user asks to:

* Create a new branch from `main`
* Commit current changes
* Push the branch
* Create a new pull request

## Instructions

1. Make sure the new branch is based on the latest `main`.
2. Create a suitable branch name based on the current changes.
3. Commit the changes with a clear commit message.
4. Push the new branch to the remote repository.
5. Create a pull request targeting `main`.
6. Return the PR link to the user.
