---
name: apidoc-command
description: Use when the user asks to run /apidoc-command:apidoc-command, or to generate an API specification HTML from an OpenAPI controller and register it in the apiService core.js download map.
---

# API Doc Command

Analyze an OpenAPI controller and its response DTOs, then generate an API specification HTML that follows the existing spec template, and register a download mapping in the apiService `core.js` (`API_DOC_PDF` map). The PDF itself is generated separately by the user later.

User input may be a controller path, a keyword, or empty.

## Target Selection

If the user provides input:

- If it is a controller file path, read that controller and its response DTOs.
- If it is a keyword, search `src/main/java/**/*Controller.java` to identify the target controller. Ask the user if several candidates match.

If the user provides no input:

- Inspect `git diff HEAD~1 HEAD --name-only` for recently added or changed `*Controller.java`.
- If ambiguous, ask the user which controller.

Do not guess. Base the document on code you directly read.

## Path Conventions (project)

- Spec HTML output: `src/main/resources/static/data_platform/docs/api/<kebab-name>-api.html`
- Template: an existing HTML in the same folder. Default `reverse-geocoding-api.html` (or any `*-api.html`).
- core.js: `src/main/resources/static/data_platform/assets/js/apiService/core.js` — the `API_CONFIGS` and `API_DOC_PDF` objects.

If these paths do not exist (different structure), ask the user for the real locations.

## Procedure

### 1) Analyze code (no guessing, read directly)

- Read the controller: request-mapping URL, HTTP method, `@RequestParam`/`@RequestHeader` params (type, required, `@Parameter` description/example), API Key auth, `@Operation` summary/description.
- Read the response DTO and its nested/Row DTOs: extract each field name, type, and `@Schema(description, example, nullable)` verbatim. Do not invent descriptions. Reflect coded-value meanings (e.g. sky 1=clear/3=cloudy) when present in `@Schema`.
- Note null / partial-match rules (`nullable=true`, empty-array defaults) and reflect them in the response examples.

### 2) Reuse the template HEAD (do NOT Read it)

The template HTML embeds base64 fonts and exceeds 1MB, so the Read tool fails on it. **Never read it whole.** Instead, slice everything before `</header>` (head + style + logo header) with a script, and reuse it — this preserves the embedded fonts (Noto Sans KR / Poppins / JetBrains Mono) and the EGIS SVG logo, which must be cloned, not regenerated.

Python skeleton (write to scratchpad and run):

```python
# -*- coding: utf-8 -*-
import io
DOCS = "<...>/static/data_platform/docs/api"
TEMPLATE = DOCS + "/reverse-geocoding-api.html"
OUT = DOCS + "/<kebab-name>-api.html"

ref = io.open(TEMPLATE, encoding="utf-8").read()
head_part, _ = ref.split("  </header>", 1)   # keep head + style + header (with logo)

# Replace only the template's title/subtitle with the new API values.
head_part = head_part.replace("<title>Reverse Geocoding API 명세</title>", "<title>NEW TITLE</title>")
head_part = head_part.replace("<h1>Reverse Geocoding API 명세</h1>", "<h1>NEW TITLE</h1>")
head_part = head_part.replace("<p>좌표(위경도) → 도로명 / 지번 주소 변환 API</p>", "<p>NEW SUBTITLE</p>")

BODY = u"""  </header>
  ... build sections per step 3 ...
</div>
</body>
</html>
"""
io.open(OUT, "w", encoding="utf-8").write(head_part + BODY)
```

The replaced strings must exactly match the chosen template's real content. If using a different template, confirm its actual title/subtitle first with a short `grep -o '<title>[^<]*</title>'` (short Bash matches are fine even though full Read is not).

### 3) Document structure (same as the template)

Follow the section layout of `reverse-geocoding-api.html`:

- `<h2>1. API 개요</h2>` — description + table (Method / URL / auth / Content-Type / CRS / data source).
- `<h2>2. 요청 (Request)</h2>`
  - `<h3>2.1 Query Parameters</h3>` — table (param / type / required(✔) / desc / example). Include `apiKey` row if required.
  - `<h3>2.2 요청 예시</h3>` — `<pre><code>` GET example + curl example (use DTO example values, `&` as `&amp;`).
- `<h2>3. 응답 (Response)</h2>`
  - `<h3>3.1 공통 응답 구조</h3>` — code / message / messageCode / data table (ApiResponse wrapper).
  - `<h3>3.2 data 구조</h3>` — top-level fields table + one `<h4>block</h4>` + field table per nested object/array. Mark nullable fields ("null when unmatched", etc.).
  - `<h3>3.3 응답 예시 (성공)</h3>` — JSON `<pre><code>` filled with real example values.
  - Add `<h3>3.4 응답 예시 (부분/미매칭)</h3>` when null/empty-array cases exist.
- `<h2>4. 응답 코드</h2>` — HTTP status table (200/400/401·403/500) + optional `<blockquote>` note.
- `<footer class="doc-footer"><span>NEW TITLE</span><span>EGIS 데이터플랫폼</span></footer>`.

Style: `<thead><tr><th>` headers; required cell as `<td style="text-align:center">✔</td>`; wrap fields/codes/coded-values in `<code>`. Mirror the template's markup patterns. Write body copy in Korean.

### 4) Register in core.js

- Find the API key in `API_CONFIGS` whose `urlPath` matches the controller's request-mapping URL (e.g. `'weather-point'`). If none, ask the user which key (or note the config entry doesn't exist yet).
- Append one line at the end of the `API_DOC_PDF` object (keep the aligned format), adding a comma to the previous last line:
  ```js
  '<apiKey>'          : '<kebab-name>-api.pdf'
  ```
- Do not create the PDF; the user adds it to the same folder later. `_toggleDocBtn` shows the download button based on this mapping.

### 5) Verify

- grep the generated HTML: new `<title>`/`<h1>`, no leftover template text (other API field names), exactly one `</html>` and one `</header>`, four `<h2>` sections.
- Confirm the new key exists in `API_DOC_PDF` and the JSON commas are valid.

## Notes

- Never Read the template HTML whole (1MB+ embedded fonts → failure). Always slice the head with a script.
- Field descriptions/types/examples/coded-value meanings come directly from `@Schema`/`@Parameter`. No guessing.
- Generate files and register the mapping only. PDF generation and git commits are the user's decision.
- If paths or the response wrapper differ from these project conventions, confirm with the user before proceeding.
