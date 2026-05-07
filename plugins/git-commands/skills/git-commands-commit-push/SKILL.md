---
name: git-commands-commit-push
description: Use when the user asks to run /git-commands:commit-push, commit and push according to COMMIT.md, or push existing committed changes without creating a PR.
---

# Git Commands Commit Push

Create a commit when needed and push the current branch using the repository's `COMMIT.md` rules.

## Workflow

1. Inspect `git status`, `git diff HEAD`, current branch, and recent commits.
2. Read `COMMIT.md` if it exists. If it does not exist, use conventional commits.
3. If the current branch is `main` or `master`, create a feature branch before committing.
4. If there are uncommitted changes, stage the relevant files except `COMMIT.md` and create one commit.
5. If there are no uncommitted changes, skip commit creation.
6. Push the branch to `origin`.

## Rules

- Do not add a `Co-Authored-By` line to the commit message.
- Do not commit `COMMIT.md`.
- Do not create a PR.
