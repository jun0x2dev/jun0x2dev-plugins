# docs-command

기능 코드를 분석해서 `docs/` 경로에 상세 기능 명세 MD 파일을 자동 생성하는 Claude Code 플러그인입니다.

## 만든 이유

기능 구현 후 문서화를 별도로 작성하는 건 번거롭고, 구현과 문서가 어긋나는 경우가 자주 생깁니다. `docs-command`는 실제 코드를 직접 읽어 분석하기 때문에 추측 없이 구현 기반의 명세를 작성합니다. 파일 경로나 키워드를 주면 연관 파일까지 탐색하고, 아무것도 안 주면 최근 커밋 diff를 기준으로 자동 분석합니다.

## 설치

```
/plugin marketplace add jun0x2dev/jun0x2dev-plugins
/plugin install docs-command
```

## 명령어

| 명령어 | 설명 |
|--------|------|
| `/docs-command:docs-command` | 기능 명세 문서 생성 |
| `/docs-command:docs-command src/controller/UserController.java` | 특정 파일 기준으로 문서 생성 |
| `/docs-command:docs-command payment` | 키워드 기준으로 관련 파일 탐색 후 문서 생성 |

## 동작 방식

1. 인자가 있으면 파일 경로 또는 키워드로 관련 파일 탐색
2. 인자가 없으면 `git diff HEAD~1 HEAD`로 최근 변경사항 분석
3. 관련 파일(Controller/Service/DAO/Mapper 등)을 직접 읽어 구현 내용 파악
4. 도메인별 하위 폴더(`docs/auth/`, `docs/payment/` 등)에 명세 MD 파일 생성
5. 커밋 여부는 사용자가 직접 결정

## 생성 문서 구조

| 섹션 | 포함 조건 |
|------|-----------|
| `## 개요` | 항상 포함 |
| `## 관련 파일` | 항상 포함 |
| `## 엔드포인트` | REST API가 있는 경우 |
| `## 응답 구조` | JSON 응답이 있는 경우 |
| `## DB 구조` | DB 테이블/쿼리가 있는 경우 |
| `## 처리 흐름` | 복잡한 처리 단계가 있는 경우 |
| `## 주요 설계 결정` | 설계 배경이 있는 경우 |
| `## 에러 코드` | 에러 처리가 있는 경우 |
| `## 알려진 제약 사항` | 미완성/한계가 있는 경우 |
