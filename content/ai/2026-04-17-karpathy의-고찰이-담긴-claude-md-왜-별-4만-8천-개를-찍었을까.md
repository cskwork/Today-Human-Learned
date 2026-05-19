+++
title = "Karpathy의 고찰이 담긴 CLAUDE.md, 왜 별 4만 8천 개를 찍었을까?"
date = "2026-04-17"
description = "Karpathy의 LLM 코딩 관찰을 70줄로 압축한 CLAUDE.md를 프로젝트에 붙이면 Claude Code의 과잉 수정, 근거 없는 가정, 범위 이탈을 눈에 띄게 줄일 수 있다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Karpathy의 LLM 코딩 관찰을 70줄로 압축한 CLAUDE.md를 프로젝트에 붙이면 Claude Code의 과잉 수정, 근거 없는 가정, 범위 이탈을 눈에 띄게 줄일 수 있다.

---

## CLAUDE.md를 왜 쓰는지 감 잡기

Claude Code에게 "함수 하나만 고쳐줘"라고 했는데 파일 다섯 개가 통째로 바뀐 경험이 있다면, 그 원인은 AI 도구 자체가 아니라 명확한 제약이 없기 때문이다. Andrej Karpathy는 2026년 1월 LLM 코딩 도구의 고질병 세 가지를 짚었다. 확인 없이 맥락을 지어내는 것, 요청에 없던 추상화를 덧붙이는 것, 건드리지 말아야 할 코드까지 "개선"하는 것이다. 개발자 Forrest Chang이 이를 CLAUDE.md라는 70줄짜리 행동 지침으로 정리해 공개했고, 2026년 4월 기준 GitHub 스타가 48,309개에 달한다. 파일 한 개짜리 저장소가 이 규모의 관심을 받은 건 그만큼 문제가 보편적이라는 뜻이다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: CLAUDE.md 적용 → Claude Code 세션 시작 시 자동 로드 → 4원칙이 행동 제약으로 작동`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| CLAUDE.md | Claude Code가 세션 시작 시 자동으로 읽는 프로젝트 규칙 파일이다. |
| Think Before Coding | 가정을 드러내고 모호하면 멈춰서 묻는 원칙이다. 침묵한 채로 틀린 방향으로 달리는 것을 막는다. |
| Simplicity First | 요청 외 기능, 추상화, 방어 코드를 금지하는 원칙이다. |
| Surgical Changes | 바꿔야 할 줄만 바꾸고, 그 변경이 만든 불필요한 임포트만 정리한다. |
| Goal-Driven Execution | 모호한 지시를 검증 가능한 목표(테스트 통과)로 변환해 실행한다. |

## 예를 들어 설명하면

Goal-Driven 원칙은 막연한 지시를 측정 가능한 조건으로 바꾼다.

```
# 모호한 지시 → 검증 가능한 목표
"입력 검증 추가" → "잘못된 입력용 테스트를 먼저 쓰고, 그 테스트를 통과시켜라"
"버그 수정"      → "버그를 재현하는 테스트를 쓴 뒤, 그 테스트를 통과시켜라"
"X 리팩터링"     → "리팩터 전후로 기존 테스트가 모두 통과하는지 확인하라"
```

적용 방법은 두 가지다. 여러 프로젝트에 공유하려면 플러그인을 설치한다.

```
/plugin marketplace add forrestchang/andrej-karpathy-skills
/plugin install andrej-karpathy-skills@karpathy-skills
```

이 프로젝트에서만 쓰려면 파일을 직접 기존 CLAUDE.md에 이어 붙인다.

```bash
curl -fsSL https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md \
  >> ./CLAUDE.md
```

## 이 단계에서 중요한 판단 기준

Surgical Changes처럼 맞물리는 원칙은 부분 적용 시 효과가 반감되므로, 일부 원칙만 발췌할 때는 의존 관계를 먼저 파악하라.

## 한 줄 요약 — 이것만 기억하면 된다

**LLM 코딩 도구에 필요한 건 더 많은 기능이 아니라 더 명확한 제약이며, CLAUDE.md 한 장이 그 제약을 실행 시점에 강제한다.**

## 나중에 더 깊게 들어가면

- CLAUDE.md를 팀 전체에 배포할 때 Plugin marketplace 권한 관리
- 4원칙을 프로젝트 컨벤션에 맞게 커스터마이징하는 방법
- 다른 AI 코딩 도구(Copilot, Cursor 등)에서 동일 원칙을 적용하는 방법

---

**원본:** [Karpathy의 고찰이 담긴 CLAUDE.md, 왜 별 4만 8천 개를 찍었을까? — https://memoryhub.tistory.com/1055](https://memoryhub.tistory.com/1055)
