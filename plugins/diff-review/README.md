# diff-review

git diff 기반으로 변경사항을 리뷰하고 수정 여부를 확인하는 Claude Code 플러그인입니다.

## 설치

```bash
/plugin install jun0x2dev/jun0x2dev-plugins/plugins/diff-review
```

## 명령어

| 명령어 | 설명 |
|--------|------|
| `/diff-review:diff-review` | git diff 기반 코드 리뷰 실행 |

## 동작 방식

1. `git diff HEAD`로 변경사항 파악
2. 필요한 경우에만 관련 라인 읽기 (토큰 절약)
3. Critical / Important / Minor 심각도로 이슈 출력
4. 수정 여부를 사용자에게 확인 후 진행

## 이슈 심각도

| 심각도 | 설명 |
|--------|------|
| Critical | 반드시 수정이 필요한 버그, 보안 문제 |
| Important | 수정을 권장하는 품질/구조 문제 |
| Minor | 참고용 개선 제안 |

