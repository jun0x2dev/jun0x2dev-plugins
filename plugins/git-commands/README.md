# git-commands

`COMMIT.md` 규칙에 따라 Git 커밋, 푸시, PR 생성을 자동화하는 Claude Code 플러그인입니다.

## 만든 이유

공식 `commit-commands` 플러그인을 사용하면 매번 아래처럼 입력해야 했습니다.
```
/commit-commands:commit @COMMIT.md 의 규칙을 보고 진행해
```

이 반복을 없애고, `COMMIT.md` 규칙을 자동으로 적용하도록 만들었습니다.
또한 공식 플러그인에는 커밋만 하고 push는 하지 않는 `commit-push` 명령어가 없어 추가했습니다.

## 공식 commit-commands와의 차이점

| 기능 | 공식 commit-commands | git-commands |
|------|:---:|:---:|
| 기본 커밋 생성 | O | O |
| COMMIT.md 규칙 자동 적용 | X | O |
| commit-push 분리 명령어 | X | O |
| 이미 커밋된 경우 push만 진행 | X | O |

- 공식 commit-commands 주소 : https://github.com/anthropics/claude-plugins-official/tree/main/plugins/commit-commands

## 설치

```bash
/plugin install jun0x2dev/jun0x2dev-plugins/plugins/git-commands
```

## 명령어

| 명령어 | 설명 |
|--------|------|
| `/git-commands:commit` | 커밋만 생성 |
| `/git-commands:commit-push` | 커밋 + push |
| `/git-commands:commit-push-pr` | 커밋 + push + PR 생성 |

## 동작 방식

- 프로젝트 루트의 `COMMIT.md` 파일을 읽어 커밋 메시지 형식, PR 규칙 등을 자동으로 적용합니다.
- `COMMIT.md`가 없으면 Claude가 기본 판단으로 커밋 메시지를 작성합니다.
- `application.yml`의 `spring.profiles.active`가 `dev`이면 해당 파일을 자동으로 스테이징에서 제외합니다.
- `commit-push-pr` 실행 시 이미 커밋된 상태면 push + PR 생성만 진행합니다.

## COMMIT.md 작성 예시

프로젝트 루트에 아래 형식으로 `COMMIT.md`를 작성하세요.

````markdown
# Commit & PR Convention

## 커밋 규칙

### 형식
```
type(scope): 설명

- 설명(선택사항)
```

### Type 종류

| type | 설명 | 예시 |
|------|------|------|
| `feat` | 새로운 기능 추가 | `feat(search): 검색 필터 기능 추가` |
| `fix` | 버그 수정 | `fix(auth): 로그인 토큰 만료 오류 수정` |
| `refactor` | 코드 리팩토링 | `refactor(map): 지도 컴포넌트 분리` |
| `chore` | 설정, 환경 변경 | `chore: 빌드 스크립트 수정` |
| `docs` | 문서 수정 | `docs: README 업데이트` |
| `style` | 코드 스타일 변경 | `style: 들여쓰기 정리` |
| `test` | 테스트 추가/수정 | `test(search): 검색 API 단위 테스트 추가` |

### 주의사항
- `main`, `master` 브랜치에서는 커밋하지 않는다
- 반드시 feature 브랜치에서 작업 후 PR로 머지
- 커밋 메시지에 `Co-Authored-By` 라인을 추가하지 않는다

---

## PR 규칙

### 제목 형식
```
[type] 한글 설명 (50자 이내)
```

### 본문 형식
```
## Summary
- 변경 이유와 내용을 한국어로 작성
- 핵심 변경사항 위주로 bullet points

## Test
변경사항을 확인한 방법 간단히 서술 (선택사항)
```

### 주의사항
- PR 제목과 본문은 한국어로 작성
- 하나의 PR은 하나의 기능/수정에 집중
- base 브랜치 확인 후 PR 생성
````
