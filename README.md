# jun0x2dev-plugins

jun0x2dev의 Claude Code 플러그인 모음입니다.

## 플러그인 목록

| 플러그인 | 설명 |
|----------|------|
| [git-commands](./plugins/git-commands) | COMMIT.md 규칙 기반 Git 커밋/푸시/PR 자동화 |
| [diff-review](./plugins/diff-review) | git diff 기반 코드 리뷰 |

## 설치

각 플러그인은 독립적으로 설치할 수 있습니다.

```bash
/plugin install jun0x2dev/jun0x2dev-plugins/plugins/git-commands
/plugin install jun0x2dev/jun0x2dev-plugins/plugins/diff-review
```

## 요구사항

- [Claude Code](https://claude.ai/code)
- PR 생성 시 [GitHub CLI (gh)](https://cli.github.com/) 필요

## License

MIT
