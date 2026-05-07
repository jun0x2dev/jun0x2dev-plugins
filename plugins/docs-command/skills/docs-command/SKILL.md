---
name: docs-command
description: Use when the user asks to run /docs-command:docs-command, generate feature documentation, or create implementation-based Markdown specs under docs/.
---

# Docs Command

Analyze the requested feature or recent changes and create a detailed Korean Markdown feature spec under `docs/`.

User input may be a file path, keyword, or empty.

## Target Selection

If the user provides input:

- If it is a file path, inspect that file and related Controller, Service, DAO, Mapper, XML, and template files.
- If it is a keyword, search `src/` for related files and inspect the relevant implementation.

If the user provides no input:

- Inspect `git diff HEAD~1 HEAD` for recent commit changes.
- Read the changed files directly.
- If the diff is missing or too small to identify the feature, ask the user for a feature name or file path.

Do not guess. Base the document on code you directly read.

## File Rules

- Create the document under a domain folder, such as `docs/auth/`, `docs/payment/`, `docs/user/`, or `docs/notification/`.
- Use concise kebab-case for the domain folder.
- Use kebab-case for the file name.
- For repeated updates to an existing feature, prefix the file name with `YYYYMMDD-`.
- Only project-wide common features may be documented directly under `docs/`.

## Document Rules

Write in Korean.

Always include:

- `## 개요`: purpose, scope, and core behavior in one or two sentences.
- `## 관련 파일`: table of related Controller, Service, DAO, Mapper, XML, template, and other files.

Include when applicable:

- `## 엔드포인트`: URL, method, request parameters, request examples.
- `## 응답 구조`: JSON example and field table.
- `## DB 구조`: table columns and DDL SQL.
- `## 처리 흐름` or `## 검증 흐름`: ASCII flow diagram.
- `## 검색 처리 로직`: algorithm, regex, branches.
- `## 주요 설계 결정`: reasoning and tradeoffs.
- `## 에러 코드`: code, HTTP status, message, condition.
- `## 알려진 제약 사항`: incomplete parts, edge cases, limitations.

## Style

- Use tables for parameters, fields, and error codes.
- Use ASCII flowcharts for process flows.
- Include SQL snippets only for key conditions.
- Explain why important design decisions exist.
- Use realistic examples that can verify behavior.
- Create `docs/` if it does not exist.
- Do not commit the generated document unless the user asks.
