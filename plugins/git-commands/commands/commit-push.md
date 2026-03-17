---
allowed-tools: Bash(git checkout:*), Bash(git add:*), Bash(git status:*), Bash(git push:*), Bash(git commit:*)
description: COMMIT.md 규칙에 따라 commit, push 생성 (PR 제외)
---

## Context

- Current git status: !`git status`
- Current git diff (staged and unstaged changes): !`git diff HEAD`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -5`
- Commit rules: !`cat COMMIT.md 2>/dev/null || echo "No COMMIT.md found, use conventional commits"`

## Your task

COMMIT.md 규칙에 따라 아래 순서로 진행하세요.

- 커밋 메시지에 `Co-Authored-By` 라인을 추가하지 않는다.
- `COMMIT.md` 파일은 커밋하지 않는다.

1. main/master 브랜치이면 새 브랜치 생성
2. 스테이징되지 않은 변경사항이 있으면 커밋 생성, 이미 커밋된 상태면 건너뜀
3. 브랜치를 origin에 push

You have the capability to call multiple tools in a single response. You MUST do all of the above in a single message. Do not use any other tools or do anything else. Do not send any other text or messages besides these tool calls.
