# oh-my-codex(OMX) — 23,000 스타 돌파한 Codex CLI 워크플로우

> **TL;DR**
> OMX는 OpenAI Codex CLI를 교체하지 않고 그 위에 표준 워크플로우, skill, 영속 상태를 얹는 얇은 레이어다.

---

## OMX를 왜 쓰는지 감 잡기

Codex CLI는 가볍고 강력하지만, 매 세션마다 컨텍스트를 처음부터 다시 설정해야 한다. 멀티 에이전트를 쓰려면 worktree를 수동으로 구성해야 하고, 훅을 붙이려면 설정 파일을 직접 편집해야 한다. OMX는 이 반복 작업을 없애기 위해 만들어졌다. Codex의 코드 생성 능력은 그대로 두고, "어떻게 일을 시킬지"를 표준화한다. Codex가 두뇌라면 OMX는 업무 매뉴얼과 사무실이다. MIT 라이선스로 2026년 2월 공개됐으며 GitHub 스타 23,000개를 넘겼다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: omx setup → 세션 시작 → skill 호출 → 결과가 .omx/ 하위에 영속 저장`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Codex CLI | OpenAI 공식 터미널 코딩 에이전트다. OMX의 실행 엔진 역할을 한다. |
| OMX (oh-my-codex) | Codex CLI 위에 워크플로우, skill, 영속 상태를 추가하는 npm 패키지다. |
| skill | OMX가 등록한 재사용 가능한 명령이다. `$deep-interview`, `$ralplan`, `$ralph`, `$team`이 핵심 4종이다. |
| .omx/ | 진행 중인 계획, 로그, 메모리가 저장되는 디렉터리다. 세션이 끊겨도 여기서 이어 시작한다. |
| git worktree | `$team` 병렬 실행 시 각 에이전트가 격리된 작업 공간을 갖게 해준다. 머지 충돌을 막는다. |

## 예를 들어 설명하면

JWT 갱신 로직을 다시 짜야 하는 상황을 가정하자. 설치 후 Codex 세션 안에서 아래 순서로 입력하면 된다.

```
$deep-interview "JWT 갱신 로직을 다시 짜고 싶다. 경계 조건이 모호하다"
$ralplan "검토된 의도를 바탕으로 안전한 구현 계획을 승인해 달라"
$ralph "승인된 계획을 끝까지 책임지고 완료까지 밀어붙여라"
```

병렬 처리가 필요하면 `$team 3:executor "승인된 계획을 3명이 병렬로 수행"` 으로 전환한다. 각 에이전트는 격리된 worktree에서 실행되므로 충돌 없이 병합된다.

설치는 두 줄이면 끝난다.

```bash
npm install -g @openai/codex oh-my-codex
omx setup
```

`omx setup`은 설정 파일, 훅, skill 묶음을 한 번에 구성하며, 기존 훅이 있어도 덮어쓰지 않는 멱등 설계다.

## 이 단계에서 중요한 판단 기준

`$deep-interview`는 요구사항이 명확하지 않을 때만 쓴다. 작업이 이미 명확하다면 `$ralplan`이나 `$ralph`부터 시작하는 것이 과잉 절차를 피하는 판단 기준이다.

## 한 줄 요약 — 이것만 기억하면 된다

**OMX는 Codex CLI의 두뇌는 그대로 두고, 반복되는 세션 설정과 워크플로우 구성을 표준화해 매번 처음부터 시작하는 피로를 없애준다.**

## 나중에 더 깊게 들어가면

- `omx hud --watch`로 실시간 멀티 에이전트 상태를 모니터링하는 방법
- `omx wiki`로 로컬 마크다운 기반 지식 베이스를 구성하는 방법
- Windows WSL2 환경에서의 tmux 대체 설정 방법

---

**원본:** [oh-my-codex(OMX), 23,000 스타 돌파한 Codex CLI 워크플로우 — https://memoryhub.tistory.com/1058](https://memoryhub.tistory.com/1058)
