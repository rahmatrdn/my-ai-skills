---
name: quick-task
description: Use when user gives a short, one-off task prompt — a small feature, bug fix, or tweak that fits in a single prompt. Triggere: "quick task", "task singkat", "bikin/ubah X" dengan scope kecil.
---

# Quick Task

Short-prompt task. Complete on its own branch, commit the result. No MR, no push.

**Announce:** "Using quick-task: branch init, task, commit."

## Step 1 — Init branch

Use the `gh-init-branch` skill to create a dedicated branch for this task. Derive the branch name and prefix from the task intent (e.g. `feat/`, `fix/`, `chore/`).

## Step 2 — Execute task

Complete the task from the user's message.

Follow the repo's existing conventions, run the project's lint/typecheck/tests when applicable, and verify your work before moving on.

## Step 3 — Commit

Stage the relevant changes and commit using the `caveman-commit` skill for the message. Commit only files related to this task. Do not push. Do not open a PR.

Report: branch name, base branch, what changed, commit message.

## Step 4 — MR (optional)

If the user asked for an MR/PR or "bikin mr", push and open the MR using the `gh-mr` skill. Otherwise skip this step and stop after commit.
