# jun0x2dev-plugins

jun0x2dev의 Claude Code 플러그인 모음입니다.

## 플러그인 목록

| 플러그인 | 설명 |
|----------|------|
| [git-commands](./plugins/git-commands) | COMMIT.md 규칙 기반 Git 커밋/푸시/PR 자동화 |
| [diff-review](./plugins/diff-review) | git diff 기반 코드 리뷰 |

## 설치

먼저 마켓플레이스를 추가합니다.

```
/plugin marketplace add jun0x2dev/jun0x2dev-plugins
```

이후 원하는 플러그인을 설치합니다.

```
/plugin install git-commands
/plugin install diff-review
```

## 요구사항

- [Claude Code](https://claude.ai/code)
- PR 생성 시 [GitHub CLI (gh)](https://cli.github.com/) 필요

## License

MIT
