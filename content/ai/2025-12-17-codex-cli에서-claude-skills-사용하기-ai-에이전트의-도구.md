+++
title = "Codex CLI에서 Claude Skills 사용하기: AI 에이전트의 공통 도구"
date = "2025-12-17"
description = "Anthropic이 만든 SKILL.md 포맷을 OpenAI Codex CLI도 채택했다. 한 번 작성한 Skill을 Claude Code와 Codex CLI 양쪽에서 그대로 재사용할 수 있다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Anthropic이 만든 SKILL.md 포맷을 OpenAI Codex CLI도 채택했다. 한 번 작성한 Skill을 Claude Code와 Codex CLI 양쪽에서 그대로 재사용할 수 있다.

---

## Skills를 왜 쓰는지 감 잡기

AI 에이전트에게 "React 컴포넌트는 함수형으로, TypeScript 인터페이스로 Props를 정의해"라고 매번 프롬프트에 쓰는 건 반복 작업이다. Skills는 이 지침을 파일로 저장해두고 에이전트가 필요할 때 꺼내 쓰게 만드는 방식이다. 프랜차이즈 운영 매뉴얼처럼, 한 번 잘 써두면 누가 실행해도 일관된 결과가 나온다.

2025년 10월 Anthropic이 Claude Code에 Skills 시스템을 도입했고, 두 달 후 OpenAI가 동일한 포맷을 Codex CLI에 조용히 채택했다. 공식 발표 없이 PR로 추가됐다는 점에서, 이 포맷이 업계 표준으로 굳어지고 있다는 신호로 읽힌다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: SKILL.md 작성 → 디렉토리에 배치 → 에이전트 실행 시 자동 참조 → 필요할 때 전체 내용 로드`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Skill | YAML 프론트매터 + 마크다운 본문으로 구성된 에이전트 지침 파일 |
| SKILL.md | Skill 파일의 고정된 이름. 폴더 안에 이 이름으로 저장해야 인식된다 |
| 프론트매터 | 파일 상단의 `---` 블록. name과 description을 여기에 쓴다 |
| 점진적 공개 | 시작 시에는 메타데이터만 로드하고, 필요할 때만 전체 내용을 읽는 방식 |
| 플랫폼 종속 | 특정 도구에만 작동하는 포맷을 쓰면 다른 도구로 이전하기 어려운 문제 |

## 예를 들어 설명하면

Codex CLI용 React Skill을 만든다면 `~/.codex/skills/react-component/SKILL.md`에 이렇게 작성한다.

```yaml
---
name: react-component
description: React 컴포넌트 작성 시 사용. TypeScript, 함수형 컴포넌트, 커스텀 훅 패턴을 따릅니다.
---
# React Component Skill

## 기본 규칙
- 함수형 컴포넌트만 사용
- Props는 TypeScript 인터페이스로 정의
- 상태 관리는 useState, useReducer 우선
```

실행 시 `codex --enable skills -m gpt-5.2`처럼 `--enable skills` 옵션이 필요하다. Claude Code에서는 기본 활성화 상태다. 기존 Claude Skills를 Codex CLI에 쓰고 싶다면 `~/.codex/skills/` 아래에 폴더째 복사하면 된다.

두 플랫폼의 주요 차이는 경로와 활성화 방식뿐이다.

| 항목 | Claude Code | Codex CLI |
|---|---|---|
| Skill 경로 | /mnt/skills/ 또는 프로젝트 내 | ~/.codex/skills/ |
| 활성화 방식 | 기본 활성화 | --enable skills 필요 |
| name 제한 | 명시적 제한 없음 | 100자 |
| description 제한 | 명시적 제한 없음 | 500자 |

## 이 단계에서 중요한 판단 기준

description은 에이전트가 "언제 이 Skill을 써야 하는지" 판단하는 유일한 단서다. 500자 제한 안에서 사용 상황을 구체적으로 적는 것이 핵심이다.

## 한 줄 요약 — 이것만 기억하면 된다

**SKILL.md 파일 하나로 Claude Code와 Codex CLI 양쪽에서 재사용 가능한 에이전트 지침을 만들 수 있다.**

## 나중에 더 깊게 들어가면

- 컨텍스트 윈도우 효율을 높이는 점진적 공개 방식의 내부 동작
- Skill 간 참조(FORMS.md처럼 다른 문서를 Skill 안에서 링크하기)
- ChatGPT Code Interpreter에서의 Skills 지원 현황

---

**원본:** [Codex CLI에서 Claude Skills 사용하기: AI 에이전트의 도구](https://memoryhub.tistory.com/930)
