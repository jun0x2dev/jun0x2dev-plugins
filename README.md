# jun0x2dev-plugins

jun0x2dev의 Claude Code / Codex 플러그인 모음입니다.

## 플러그인 목록

| 플러그인 | 설명 |
|----------|------|
| [git-commands](./plugins/git-commands) | COMMIT.md 규칙 기반 Git 커밋/푸시/PR 자동화 |
| [diff-review](./plugins/diff-review) | git diff 기반 코드 리뷰 |
| [docs-command](./plugins/docs-command) | 기능 코드 분석 기반 docs/ 명세 문서 생성 + 작성 후 코드 대조 검증 |

## Claude Code 설치

먼저 마켓플레이스를 추가합니다.

```
/plugin marketplace add jun0x2dev/jun0x2dev-plugins
```

이후 원하는 플러그인을 설치합니다.

```
/plugin install git-commands
/plugin install diff-review
/plugin install docs-command
```

## Codex 사용

Codex는 Claude Code의 `commands/*.md` slash command를 직접 호출하지 않고, 플러그인 안의 `skills/*/SKILL.md`를 통해 동작합니다.

이 저장소는 각 플러그인에 `.codex-plugin/plugin.json`와 Codex skill을 함께 제공합니다.

| Claude Code 명령어 | Codex skill |
|--------------------|-------------|
| `/git-commands:commit` | `$git-commands-commit` |
| `/git-commands:commit-push` | `$git-commands-commit-push` |
| `/git-commands:commit-push-pr` | `$git-commands-commit-push-pr` |
| `/diff-review:diff-review` | `$diff-review` |
| `/docs-command:docs-command` | `$docs-command` |

Codex에 이미 설치한 뒤 이 변경사항을 받은 경우, 플러그인을 재설치하거나 Codex 세션을 새로 시작해야 새 skill 목록이 반영될 수 있습니다.
