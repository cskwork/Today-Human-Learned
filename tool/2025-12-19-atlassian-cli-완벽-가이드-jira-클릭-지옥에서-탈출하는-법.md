# Atlassian CLI: Jira 반복 작업을 터미널에서 자동화하는 법

> **TL;DR**
> Atlassian 공식 CLI(ACLI)는 Jira Cloud의 이슈 생성, 상태 변경, 대량 수정을 명령줄 한 줄로 처리하며, 2025년 5월부터 모든 Jira Cloud 플랜에 무료로 포함됐다.

---

## ACLI를 왜 쓰는지 감 잡기

Jira에서 이슈 100개의 상태를 일괄 변경한다고 하자. GUI에서는 한 개씩 열고 드롭다운을 바꾸는 과정을 100번 반복해야 한다. 터미널에서는 JQL 조건을 한 줄로 쓰면 끝난다.

ACLI는 Atlassian이 직접 만든 공식 CLI 도구다. 기존에는 Appfire(구 Bob Swift)의 서드파티 솔루션이 유일한 선택지였고 Java와 유료 라이선스가 필요했다. 공식 ACLI는 독립 바이너리라 별도 런타임 없이 설치하고, 모든 Jira Cloud 플랜에서 무료로 쓸 수 있다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: 설치 → 인증 → acli jira [명령어] [옵션] → Jira Cloud API 호출 → 결과 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| JQL (Jira Query Language) | Jira 이슈를 SQL처럼 조회하는 쿼리 문법. `project = TEAM AND status = 'To Do'` 형식이다. |
| Work Item | ACLI에서 이슈(Issue)를 지칭하는 공식 용어. `acli jira workitem` 명령 계열이 여기에 해당한다. |
| OAuth | 브라우저로 한 번 로그인하면 토큰을 자동 관리하는 인증 방식. 개인 사용에 편리하다. |
| API 토큰 | Atlassian 계정 설정에서 발급받는 비밀 키. 스크립트나 CI/CD 환경에 적합하다. |
| Transition | 이슈의 상태를 변경하는 작업. "To Do → In Progress → Done" 같은 워크플로 이동이다. |

## 예를 들어 설명하면

스프린트가 끝날 때 미완료 이슈를 다음 스프린트로 자동 이동하는 스크립트다.

```bash
#!/bin/bash
# 스프린트 종료 시 미완료 이슈 이동
CURRENT="Sprint 15"
NEXT="Sprint 16"

acli jira workitem edit \
  --jql "project = TEAM AND sprint = '$CURRENT' AND status != Done" \
  --sprint "$NEXT"
```

이 스크립트를 크론잡이나 CI/CD 파이프라인에 넣으면 스프린트 전환 작업이 완전히 자동화된다. 단일 이슈 상태 변경은 이렇게 쓴다.

```bash
acli jira workitem transition --key "TEAM-42" --status "Done"
```

## 이 단계에서 중요한 판단 기준

| 사용 패턴 | 권장 인증 | 주의점 |
|---|---|---|
| 개인 로컬 사용 | OAuth (`--web`) | 브라우저 필요, CI/CD 불가 |
| 스크립트/자동화 | API 토큰 | 토큰 파일 권한 관리 필요 |
| JSON 출력 파이프 | `--json` 플래그 추가 | `jq`와 함께 쓰면 유용 |

## 한 줄 요약 — 이것만 기억하면 된다

**ACLI는 JQL과 결합하면 수백 개 이슈를 한 줄 명령으로 처리할 수 있어, 반복적인 Jira 작업을 스크립트로 완전 자동화할 수 있다.**

## 나중에 더 깊게 들어가면

- `--json` 출력과 `jq`를 조합해 대시보드 데이터 파이프라인 구성하기
- ACLI를 GitHub Actions 워크플로에 통합해 PR 머지 시 Jira 이슈 자동 완료 처리하기
- Rovo Dev CLI를 이용해 터미널에서 AI 에이전트와 코드 작업 및 Jira 이슈 동시 처리하기

---

**원본:** [Atlassian CLI 완벽 가이드: Jira 클릭 지옥에서 탈출하는 법 — https://memoryhub.tistory.com/940](https://memoryhub.tistory.com/940)
