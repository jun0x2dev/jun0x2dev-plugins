---
allowed-tools: Bash, Read, Glob, Grep, Write, Edit
description: OpenAPI 컨트롤러와 DTO를 분석해서 참고 양식 기반 API 명세 HTML을 생성하고 apiService core.js에 등록하는 스킬
---

OpenAPI 컨트롤러와 응답 DTO를 분석해서, 기존 명세 HTML 양식을 그대로 따르는 API 명세 HTML을 생성하고, `apiService`의 `core.js`(`API_DOC_PDF` 맵)에 다운로드 매핑을 등록해줘. PDF는 사용자가 별도로 생성해서 나중에 넣는다.

사용자 입력: $ARGUMENTS

## 입력 해석

`$ARGUMENTS`는 다음 중 하나:
- **컨트롤러 파일 경로** (예: `src/main/java/.../weather/controller/WeatherPointController.java`)
- **키워드** (예: `weather-point`, `parking`) — `src/main/java/**/*Controller.java`를 Grep/Glob으로 탐색해 대상 컨트롤러 특정. 후보가 여럿이면 사용자에게 확인.
- **비어 있음** — `git diff HEAD~1 HEAD --name-only`로 최근 추가/변경된 `*Controller.java`를 대상으로. 애매하면 사용자에게 물어볼 것.

## 경로 규칙 (프로젝트 관례)

- 명세 HTML 출력: `src/main/resources/static/data_platform/docs/api/<kebab-name>-api.html`
- 참고 양식(템플릿): 같은 폴더의 기존 HTML. 기본값 `reverse-geocoding-api.html` (없으면 폴더 내 아무 `*-api.html`).
- core.js: `src/main/resources/static/data_platform/assets/js/apiService/core.js` — 상단 `API_CONFIGS` 객체와 `API_DOC_PDF` 객체.

이 경로들이 프로젝트에 없으면(다른 구조면) 사용자에게 실제 위치를 확인할 것.

## 절차

### 1) 코드 분석 (추측 금지, 반드시 직접 읽기)
- 대상 컨트롤러를 Read: `@RequestMapping`/`@GetMapping` URL, HTTP 메서드, `@RequestParam`/`@RequestHeader` 파라미터(타입·필수여부·`@Parameter` 설명·example), API Key 인증 여부, `@Operation` summary/description 파악.
- 응답 DTO와 그 내부 클래스/Row DTO를 Read: 각 필드의 이름·타입·`@Schema(description, example, nullable)`를 그대로 추출. **필드 설명·예시는 지어내지 말고 `@Schema`에서 가져온다.** 코드값 의미(예: sky 1맑음/3구름많음)도 `@Schema`에 있으면 반영.
- `_yn` 접미사, `nullable=true`, 빈 배열 기본값 등 null/부분매칭 규칙을 파악(응답 예시에 반영).

### 2) 템플릿 HEAD 재사용 (중요 — Read 금지)
템플릿 HTML은 **임베드 폰트(base64)로 1MB가 넘어 Read 툴로 열면 실패**한다. 절대 통째로 읽지 말 것.
대신 **스크립트로 `</header>` 앞부분(head+style+로고 헤더)만 잘라 재사용**한다. 이 head에는 Noto Sans KR/Poppins/JetBrains Mono 임베드 폰트와 EGIS SVG 로고가 들어 있어 **새로 생성하면 안 되고 반드시 클론**해야 한다.

파이썬 스크립트 골격(스크래치패드에 작성 후 실행):

```python
# -*- coding: utf-8 -*-
import io
DOCS = "<...>/static/data_platform/docs/api"
TEMPLATE = DOCS + "/reverse-geocoding-api.html"
OUT = DOCS + "/<kebab-name>-api.html"

ref = io.open(TEMPLATE, encoding="utf-8").read()
head_part, _ = ref.split("  </header>", 1)   # head+style+헤더(로고 포함) 확보

# 템플릿의 제목/부제만 새 API 값으로 치환 (문자열은 템플릿 실제 값과 정확히 일치시킬 것)
head_part = head_part.replace("<title>Reverse Geocoding API 명세</title>", "<title>새 제목</title>")
head_part = head_part.replace("<h1>Reverse Geocoding API 명세</h1>", "<h1>새 제목</h1>")
head_part = head_part.replace("<p>좌표(위경도) → 도로명 / 지번 주소 변환 API</p>", "<p>새 부제</p>")

BODY = u"""  </header>
  ... 아래 4) 문서 구조대로 작성 ...
</div>
</body>
</html>
"""
io.open(OUT, "w", encoding="utf-8").write(head_part + BODY)
```

> 치환 대상 문자열(`<title>...`, `<h1>...`, `<p>...`)은 사용하는 템플릿의 실제 내용과 **정확히** 일치해야 한다. 다른 템플릿을 쓰면 그 파일의 실제 제목/부제 문자열을 Grep으로 먼저 확인하고 치환값을 맞춘다. (head는 Read 금지지만 `grep -o '<title>[^<]*</title>'` 등 짧은 매칭은 Bash로 확인 가능.)

### 3) 문서 구조 (참고 양식과 동일하게)
`reverse-geocoding-api.html`의 섹션 구성을 그대로 따른다:

- `<h2>1. API 개요</h2>` — 설명 문단 + 표(Method / URL / 인증 / Content-Type / 좌표계 / 데이터 출처 등)
- `<h2>2. 요청 (Request)</h2>`
  - `<h3>2.1 Query Parameters</h3>` — 표(파라미터 / 타입 / 필수(✔) / 설명 / 예시). API Key 필수면 `apiKey` 행 포함.
  - `<h3>2.2 요청 예시</h3>` — `<pre><code>` GET 예시 + curl 예시 (예시 좌표는 DTO의 example 값 사용, `&`는 `&amp;`)
- `<h2>3. 응답 (Response)</h2>`
  - `<h3>3.1 공통 응답 구조</h3>` — code / message / messageCode / data 표 (ApiResponse 래퍼)
  - `<h3>3.2 data 구조</h3>` — 최상위 필드 표 + 중첩 객체/배열마다 `<h4>블록명</h4>` + 필드 상세 표(필드 / 타입 / 설명 / 예시). nullable 필드는 "매칭 실패 시 null" 등 명시.
  - `<h3>3.3 응답 예시 (성공)</h3>` — 실제 example 값으로 채운 JSON `<pre><code>`
  - null/빈배열 케이스가 있으면 `<h3>3.4 응답 예시 (부분/미매칭)</h3>` 추가
- `<h2>4. 응답 코드</h2>` — HTTP Status 표(200/400/401·403/500) + 필요 시 `<blockquote>` 주석
- `<footer class="doc-footer"><span>새 제목</span><span>EGIS 데이터플랫폼</span></footer>` 로 마무리

스타일 규칙: 표 헤더 `<thead><tr><th>...`, 필수 표시는 `<td style="text-align:center">✔</td>`, 코드/필드/코드값은 `<code>`로 감싼다. 참고 양식의 마크업 패턴을 그대로 모방.

### 4) core.js 등록
- `core.js`의 `API_CONFIGS`에서 대상 API의 키를 찾는다: `urlPath`가 컨트롤러의 `@RequestMapping` URL과 일치하는 항목의 key(예: `'weather-point'`). 없으면 사용자에게 어떤 key인지 확인(또는 API_CONFIGS 항목이 아직 없다고 안내).
- `API_DOC_PDF` 객체 **마지막 항목 뒤에** 한 줄 추가(정렬 형식 유지):
  ```js
  '<apiKey>'          : '<kebab-name>-api.pdf'
  ```
  직전 줄 끝에 콤마 추가하는 것을 잊지 말 것. Edit 툴로 마지막 항목 라인을 정확히 교체.
- PDF 파일 자체는 만들지 않는다(사용자가 나중에 같은 폴더에 넣음). `_toggleDocBtn`이 이 매핑을 보고 다운로드 버튼을 노출하므로, 매핑만 있으면 PDF 추가 즉시 동작한다.

### 5) 검증
- 생성된 HTML을 Bash grep으로 점검: `<title>`/`<h1>`가 새 값인지, 템플릿 원본 텍스트(예: 다른 API의 필드명)가 남아있지 않은지, `</html>` 1개·`</header>` 1개인지, `<h2>` 4개 섹션이 있는지.
- core.js 변경 후 `API_DOC_PDF`에 새 key가 있고 JSON 콤마가 올바른지 확인.

## 주의사항

- **템플릿 HTML은 절대 Read로 열지 말 것**(1MB+ 임베드 폰트 → 실패). 반드시 스크립트로 head만 slice.
- 필드 설명·타입·예시·코드값 의미는 코드의 `@Schema`/`@Parameter`에서 **직접 읽어** 작성(추측 금지, 실제 구현과 다른 내용 금지).
- 파일 생성/등록까지만 수행하고 **PDF 생성·git 커밋은 사용자가 결정**.
- 경로/래퍼 구조가 이 프로젝트 관례와 다르면 진행 전 사용자에게 확인.
