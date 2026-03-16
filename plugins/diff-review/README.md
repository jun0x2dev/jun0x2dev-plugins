# diff-review

git diff 기반으로 변경사항을 리뷰하고 수정 여부를 확인하는 Claude Code 플러그인입니다.

## 만든 이유

내장 스킬인 `/simplify`는 코드 품질/재사용성 개선에 특화되어 있고 수정까지 자동으로 진행합니다. 반면 `diff-review`는 변경사항 전체를 버그, 보안, 코드 품질, 재사용성 관점에서 심각도별로 분류해 리뷰하고, 수정 여부를 사용자가 직접 결정할 수 있도록 만들었습니다.

`/simplify`의 자동 수정 기능은 포함하지 않으며, 목적 자체가 다릅니다.

## 설치

```
/plugin marketplace add jun0x2dev/jun0x2dev-plugins
/plugin install diff-review
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

