# spec-doc

코드 파일을 분석하여 상세 문서를 자동 생성하는 플러그인입니다.

## 사용법

```
/api-doc:api-doc @{파일경로}
/api-doc:api-doc @{파일경로} {특정 기능 설명}
```

### 예시

```
# 파일 내 모든 기능 문서화
/api-doc:api-doc @src/user/UserController.java

# 특정 기능만 문서화
/api-doc:api-doc @src/user/UserController.java 회원가입 API
```

## 동작 방식

1. 멘션된 파일 분석 (엔드포인트, 함수 시그니처, 파라미터 등)
2. import, 함수 호출, 의존성을 따라 연관 파일 자동 탐색
3. 상세 마크다운 문서 생성
4. 저장 여부 확인 후 `docs/{도메인}/{기능명}-{유형}.md` 로 저장

## 생성 문서 구성

- 개요
- 사용법 (코드 유형에 따라 엔드포인트/함수 시그니처/실행 조건 등)
- 처리 로직 (분기 흐름, ASCII 플로우차트)
- 관련 파일
- 제약 사항 및 주의사항 (TODO 마커로 개발자가 채울 부분 표시)

## 저장 경로 규칙

| 코드 유형 | 파일명 형식 | 예시 |
|----------|------------|------|
| HTTP API | `{기능명}-api.md` | `docs/user/create-user-api.md` |
| 유틸/헬퍼 | `{기능명}-util.md` | `docs/common/date-format-util.md` |
| 배치/스케줄러 | `{기능명}-batch.md` | `docs/batch/sync-user-batch.md` |
| 그 외 | `{기능명}.md` | `docs/order/order-state.md` |

## 설치

```bash
claude plugins install jun0x2dev/jun0x2dev-plugins
```
