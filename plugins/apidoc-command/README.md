# apidoc-command

OpenAPI 컨트롤러와 응답 DTO를 분석해서, 기존 명세 양식을 그대로 따르는 **API 명세 HTML**을 생성하고 `apiService`의 `core.js`(`API_DOC_PDF` 맵)에 다운로드 매핑까지 등록하는 Claude Code 플러그인입니다.

## 만든 이유

이 프로젝트는 OpenAPI 하나를 추가할 때마다 `docs/api/`에 명세 HTML을 만들고, `apiService` 화면의 명세서 다운로드 버튼에 매핑을 등록하는 작업이 반복됩니다. 명세 HTML은 임베드 폰트(base64)로 1MB가 넘어 손으로 다루기 어렵고, 필드 표를 DTO와 일일이 맞추는 것도 번거롭습니다. `apidoc-command`는 컨트롤러·DTO를 직접 읽어 `@Schema`/`@Parameter` 기준으로 표를 채우고, 참고 양식의 `<head>`(폰트·로고)를 그대로 클론해 일관된 문서를 만듭니다.

## 설치

```
/plugin marketplace add jun0x2dev/jun0x2dev-plugins
/plugin install apidoc-command
```

## 명령어

| 명령어 | 설명 |
|--------|------|
| `/apidoc-command:apidoc-command src/main/java/.../WeatherPointController.java` | 특정 컨트롤러로 명세 생성 |
| `/apidoc-command:apidoc-command weather-point` | 키워드로 컨트롤러 탐색 후 생성 |
| `/apidoc-command:apidoc-command` | 최근 커밋에 추가된 컨트롤러 기준으로 생성 |

## 동작 방식

1. 인자(컨트롤러 경로 / 키워드 / 없음→최근 diff)로 대상 컨트롤러 특정
2. 컨트롤러 + 응답 DTO를 직접 읽어 URL·파라미터·응답 필드(`@Schema`/`@Parameter`) 추출
3. 참고 양식(`reverse-geocoding-api.html`)의 `<head>`(임베드 폰트·EGIS 로고)를 스크립트로 잘라 재사용
4. `1. 개요 / 2. 요청 / 3. 응답 / 4. 응답 코드` 4개 섹션으로 명세 HTML 생성 → `docs/api/<name>-api.html`
5. `core.js`의 `API_DOC_PDF`에 `'<apiKey>' : '<name>-api.pdf'` 매핑 등록
6. PDF 생성과 git 커밋은 사용자가 직접 결정

## 핵심 특징

- **임베드 폰트 보존**: 1MB+ 템플릿을 통째로 읽지 않고 `</header>` 기준 head만 클론 → 폰트·로고 그대로 유지
- **추측 없는 필드 표**: 설명·타입·예시·코드값 의미를 DTO의 `@Schema`에서 직접 추출
- **양식 일관성**: 기존 명세서와 동일한 마크업/표 구조/푸터
