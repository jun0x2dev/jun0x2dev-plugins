---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git commit:*)
description: COMMIT.md 규칙에 따라 git commit 생성
---

## Context

- Current git status: !`git status`
- Current git diff (staged and unstaged changes): !`git diff HEAD`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -10`
- Commit rules: !`cat COMMIT.md`

## Your task

COMMIT.md 규칙에 따라 커밋을 생성하세요.

You have the capability to call multiple tools in a single response. Stage and create the commit using a single message. Do not use any other tools or do anything else. Do not send any other text or messages besides these tool calls.
