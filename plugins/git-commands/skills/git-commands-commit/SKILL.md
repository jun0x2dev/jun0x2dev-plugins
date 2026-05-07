---
name: git-commands-commit
description: Use when the user asks to run /git-commands:commit, commit according to COMMIT.md, or create a Git commit while excluding COMMIT.md from the commit.
---

# Git Commands Commit

Create a Git commit using the repository's `COMMIT.md` rules.

## Workflow

1. Inspect `git status`, `git diff HEAD`, current branch, and recent commits.
2. Read `COMMIT.md` if it exists. If it does not exist, use conventional commits.
3. Stage the relevant changed files, but do not stage `COMMIT.md`.
4. Create one commit that follows the commit rules.

## Rules

- Do not add a `Co-Authored-By` line to the commit message.
- Do not commit `COMMIT.md`.
- If there are no changes to commit, report that clearly.
