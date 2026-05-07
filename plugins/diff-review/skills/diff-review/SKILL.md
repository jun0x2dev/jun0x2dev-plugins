---
name: diff-review
description: Use when the user asks to run /diff-review:diff-review, review current git diff changes, or find issues in uncommitted changes before optionally fixing them.
---

# Diff Review

Review the current `git diff HEAD` and ask the user before applying any fixes.

## Workflow

1. Run `git diff HEAD`.
2. If there are no changed files, tell the user and stop.
3. Read `CLAUDE.md` if it exists and include its conventions in the review criteria.
4. Read only the relevant line ranges needed to verify issues. Avoid reading whole files unless necessary.
5. Report review findings without editing files.
6. Ask the user which fixes to apply.
7. Apply fixes only after the user chooses an option.

## Review Criteria

Look for:

- Bugs: incorrect logic, missing exception handling, edge cases.
- Security: injection, exposed secrets, authentication or authorization problems.
- Code quality: unnecessary complexity, duplication, hard-to-read structure.
- Reusability: logic that should be shared, hard-coded values.

Score confidence from 0 to 100:

- `0`: false positive.
- `25`: uncertain and not verifiable.
- `50`: likely real but low impact or uncommon.
- `75`: likely and important.
- `100`: certain and immediately actionable.

Do not report findings below confidence `50`.

## Output Format

Use this format for each issue:

```markdown
### [Critical / Important / Minor] Title
- File: `path:line`
- Details: Problem explanation in one or two lines.
- Suggestion: Improvement in one or two lines.
```

If there are no issues, say `이슈 없음`.

After reporting findings, ask:

```text
수정할까요?
1. 전체 수정
2. Critical만 수정
3. 직접 선택
4. 수정하지 않음
```
