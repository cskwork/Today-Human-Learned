# Ralph Wiggum Loop: AI가 스스로 반복하며 코딩하게 만드는 법

> **TL;DR**
> AI 코딩 에이전트를 무한 루프에 넣어 작업 완료까지 자동 반복시키는 기법으로, 완벽한 한 번의 응답 대신 끈질긴 반복이 더 강력하다.

---

## Ralph Wiggum Loop를 왜 쓰는지 감 잡기

AI 코딩 도구를 써봤다면 이 패턴에 익숙할 것이다. Claude에게 작업을 시키면 에러가 난다. 다시 지시하면 또 에러가 난다. 이 과정을 개발자가 직접 반복해야 한다는 것이 기존 방식의 병목이다.

Ralph Wiggum Loop는 이 반복을 자동화한다. 호주 개발자 Geoffrey Huntley가 2025년 고안한 방법으로, 핵심은 단 한 가지다. AI가 작업을 끝내려 할 때 완료 조건이 충족되지 않으면 같은 프롬프트를 다시 주입한다.

심슨 가족의 Ralph Wiggum 캐릭터처럼, 멍청해 보이지만 포기하지 않는 끈기가 핵심이다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 작업 시작 → Claude 실행 → 완료 조건 확인 → 미충족 시 재시작 → 반복`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Stop Hook | Claude가 종료하려는 순간을 가로채서 특정 동작을 실행하는 장치 |
| 완료 조건 (completion promise) | AI가 출력해야만 루프가 끝나는 특수 문자열 (예: `DONE`) |
| 자기 참조적 피드백 루프 | AI가 자신이 이전에 만든 코드를 보면서 개선하는 구조 |
| max-iterations | 루프 최대 반복 횟수. 설정하지 않으면 무한 실행된다 |
| git 히스토리 | 각 반복이 끝날 때 남는 코드 변경 기록. 다음 반복에서 이걸 보며 이어간다 |

## 예를 들어 설명하면

Jest 테스트를 Vitest로 마이그레이션하는 작업을 밤새 자동으로 돌리고 싶다면:

```bash
/ralph-loop "모든 Jest 테스트를 Vitest로 마이그레이션하라.
완료되면 <promise>COMPLETE</promise>를 출력하라."
--max-iterations 30
--completion-promise "COMPLETE"
```

Claude는 각 반복에서 이전 반복이 수정한 파일과 git 커밋 내역을 확인한 뒤 작업을 이어간다. 새로 시작하는 게 아니라, 자신이 만든 코드를 검토하고 개선한다.

원본 bash 방식은 더 단순하다:

```bash
while :; do cat PROMPT.md | claude-code; done
```

## 이 단계에서 중요한 판단 기준

완료 조건을 명확히 정의하고, `--max-iterations`를 반드시 설정한다. 이 두 가지가 없으면 루프가 수렴하지 않거나 예상치 못한 API 비용이 발생한다.

## 한 줄 요약 — 이것만 기억하면 된다

**AI 코딩은 완벽한 한 번이 아니라 명확한 완료 조건 아래 끈질기게 반복하는 구조가 더 강력하다.**

## 나중에 더 깊게 들어가면

- Stop Hook의 구체적 구현 방법과 커스터마이징
- Ralphy처럼 git worktree를 이용한 병렬 에이전트 실행
- 대규모 코드베이스에서 API 비용을 통제하는 예산 설정 전략

---

**원본:** Ralph Wiggum Loop: 5줄 bash로 AI가 밤새 코딩하게 만드는 법 — [https://memoryhub.tistory.com/977](https://memoryhub.tistory.com/977)
