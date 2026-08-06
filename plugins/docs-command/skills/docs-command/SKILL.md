---
name: docs-command
description: Use when the user asks to run /docs-command:docs-command, generate feature documentation, or create implementation-based Markdown specs under docs/.
---

# Docs Command

Analyze the requested feature or recent changes, create a detailed Korean Markdown feature spec under `docs/`, then verify the spec against the source code and fix any mismatch found.

User input may be a file path, keyword, or empty.

## Phase 1: Target Selection

If the user provides input:

- If it is a file path, inspect that file and related Controller, Service, DAO, Mapper, XML, and template files.
- If it is a keyword, search `src/` for related files and inspect the relevant implementation.

If the user provides no input:

- Inspect `git diff HEAD~1 HEAD` for recent commit changes.
- Read the changed files directly.
- If the diff is missing or too small to identify the feature, ask the user for a feature name or file path.

Do not guess. Base the document on code you directly read.

## Phase 2: Pick a Template

Choose based on what the document is for. Ask the user if it's ambiguous.

- **Spec template** — documenting how a feature currently works (new feature, full analysis of an existing feature, post-refactor architecture writeup).
- **Changelog template** — documenting what was fixed and why (bug fix, a specific issue, or diff-based documentation with no explicit target).

## File Rules

- Create the document under a domain folder, such as `docs/auth/`, `docs/payment/`, `docs/user/`, or `docs/notification/`.
- Use concise kebab-case for the domain folder.
- Use kebab-case for the file name.
- For repeated updates to an existing feature, prefix the file name with `YYYYMMDD-`.
- Only project-wide common features may be documented directly under `docs/`.

## Phase 3: Write the Document

Write in Korean.

### Spec template sections

Always include:

- `## 개요`: purpose, scope, and core behavior in one or two sentences, followed by a short **요약** bullet list (4-6 bullets) covering input → processing → output at a glance.
- `## 관련 파일`: table of related Controller, Service, DAO, Mapper, XML, template, and frontend files.

Include when applicable:

- `## 엔드포인트`: URL, method, request parameter table (param/location/required/type/description), request examples.
- `## 응답 구조`: JSON example plus field tables split into subsections (`###`) per response group.
- `## 처리 흐름`: ASCII flow diagram covering steps, branches, and failure behavior.
- `## 사용 테이블`: table of DB tables used — schema, role, join/matching key. Include an index-status sub-table when relevant (see DB Schema Verification below).
- `## 핵심 SQL`: the most important query verbatim, with reasoning for why it's written that way.
- `## 검색 처리 로직`: use instead of/alongside SQL section when the core logic is an algorithm, regex, or branching rules.
- `## 응답 코드` / `## 에러 코드`: code, HTTP status, message, condition table.
- `## 알려진 제약 사항`: incomplete parts, edge cases, limitations.
- `## 히스토리`: one line per milestone, from initial implementation to later extensions, for features updated repeatedly over time.

### Changelog template sections

Always include:

- `## 개요`: what was fixed and why, in one or two sentences.
- `## 무엇이 문제였나`: symptom plus root cause, backed by code references (include a repro example if available).
- `## 어떻게 수정했나`: table of fixes (# / change / effect).
- `## 관련 파일`: table of layer / file / what changed.

Include when applicable:

- `## 주요 설계 결정`: why this approach, and alternatives that were rejected and why.
- `## 동작 결과`: list of cases actually verified (with execution evidence if possible).
- `## 알려진 제약 사항`.

## Style

- Use tables for parameters, fields, error codes, and tables used.
- Use ASCII flowcharts for process flows.
- Include SQL snippets only for key conditions.
- Explain why important design decisions exist.
- Use realistic examples that can verify behavior.
- Do not add unrequested extra sections (e.g., "differences from the source spec doc", detailed frontend UI walkthroughs) up front — ask the user first if such a section seems useful.
- Create `docs/` if it does not exist.

## Phase 4: Verify

After writing the document, re-read the relevant source files again instead of trusting what Phase 1-3 assumed, and cross-check every factual claim in the document:

- Does the endpoint URL/HTTP method match `@RequestMapping`/`@GetMapping` etc.?
- Do request parameter names, required-ness, and types match the actual declarations?
- Do response field names, types, and descriptions match the actual DTO/`@Schema`?
- Do SQL conditions, joins, and column names match the Mapper XML verbatim (any SQL quoted in the doc must be an exact excerpt)?
- Do branch conditions (e.g., when each error code fires) match the service logic?
- Do file paths referenced in the document actually exist?

**DB schema verification**: if the project has a tool that can query the database directly (an MCP tool, etc.), use it to confirm tables/columns/indexes actually exist. If no such tool is available, verify only against DDL files or code, and for anything unverifiable (e.g., actual index existence) do not assert it — either note it as "not verifiable from code" or omit that claim entirely.

Fix any mismatch found **immediately**, without asking the user first — the target being verified is the document you just wrote, and this is a factual cross-check, not a judgment call. After fixing, report a short summary:

```
## 검증 결과
- 확인함: N건
- 수정함: N건
  - <what was changed, one line each>
- 확인 불가(DB 도구 없음 등): N건
```

If there was no mismatch, just say "검증 완료 — 불일치 없음".

## Notes

- Read code directly before writing — never guess or write anything that contradicts the actual implementation.
- Note incomplete parts under `## 알려진 제약 사항`.
- Create `docs/` automatically if it doesn't exist.
- Only create/edit the file — leave committing to the user.
