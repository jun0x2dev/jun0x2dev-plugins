---
name: git-commands-commit-push-pr
description: Use when the user asks to run /git-commands:commit-push-pr, commit, push, and create a pull request according to COMMIT.md.
---

# Git Commands Commit Push PR

Create a commit when needed, push the current branch, and open a PR using the repository's `COMMIT.md` rules.

## Workflow

1. Inspect `git status`, `git diff HEAD`, current branch, and recent commits.
2. Read `COMMIT.md` if it exists. If it does not exist, use conventional commits.
3. If the current branch is `main` or `master`, create a feature branch before committing.
4. If there are uncommitted changes, stage the relevant files except `COMMIT.md` and create one commit.
5. If there are no uncommitted changes, skip commit creation.
6. Push the branch to `origin`.
7. Create a PR with `gh pr create`.

## Rules

- Do not add a `Co-Authored-By` line to the commit message.
- Do not commit `COMMIT.md`.
- Write the PR title and body according to `COMMIT.md` when PR rules are present.
