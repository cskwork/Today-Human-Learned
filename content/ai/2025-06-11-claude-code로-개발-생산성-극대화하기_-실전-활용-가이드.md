+++
title = "Claude Code 실전 활용 — 반복 작업을 위임하고 판단에 집중하기"
date = "2025-06-11"
description = "Claude Code는 코드베이스 탐색, 버그 수정, 테스트 작성, 레거시 현대화를 자연어 명령으로 처리한다 — 단, 결과는 반드시 직접 검증해야 한다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Claude Code는 코드베이스 탐색, 버그 수정, 테스트 작성, 레거시 현대화를 자연어 명령으로 처리한다 — 단, 결과는 반드시 직접 검증해야 한다.

---

## Claude Code 실전 활용을 왜 배우는지 감 잡기

Claude Code를 설치하고 나서 "어떤 작업에 쓰지?" 막막한 경우가 많다. 도구 자체보다 사용 패턴을 모르는 것이 문제다.

Claude Code가 빛을 발하는 작업은 뚜렷하다. 신규 코드베이스 파악, 에러 메시지 디버깅, 레거시 코드 리팩토링, 테스트 자동 생성이다. 이 네 가지는 모두 시간이 많이 걸리지만 판단보다 탐색이 주인 작업이라 AI 위임에 적합하다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: 명확한 지시 → Claude가 코드베이스 탐색 → 수정/생성 → 개발자가 검증`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Extended Thinking | "think hard" 같은 키워드로 Claude가 대안을 더 깊이 검토하도록 유도하는 방식 |
| MCP (Model Context Protocol) | Claude를 외부 데이터 소스(DB, API)에 연결하는 표준 인터페이스 |
| CLAUDE.md | 프로젝트별 규칙, 명령어, 컨텍스트를 Claude에게 알리는 마크다운 설정 파일 |
| 슬래시 명령어 | `.claude/commands/` 폴더에 정의한 반복 작업 템플릿 (`/project:optimize` 형태로 호출) |
| `--continue` / `--resume` | 이전 대화를 이어받아 컨텍스트를 유지하면서 작업을 재개하는 플래그 |

## 예를 들어 설명하면

테스트가 없는 함수를 찾아 유닛 테스트를 자동 생성하는 일반적인 시나리오다.

```bash
# 테스트 없는 함수 탐색 및 테스트 생성
claude "테스트가 없는 함수들을 찾아서 유닛 테스트를 작성해줘"

# 복잡한 성능 문제에 Extended Thinking 적용
claude "이 성능 문제를 think harder — 해결 방안을 제시해줘"

# CI/CD에서 코드 리뷰 자동화
git diff | claude --print "이 변경사항을 리뷰해줘"

# 여러 작업을 병렬로 — Git worktree 활용
git worktree add ../feature-branch feature-1
cd ../feature-branch && claude "이 기능을 구현해줘"
```

PostgreSQL 같은 외부 DB를 연결할 때는 MCP를 쓴다.

```bash
claude mcp add postgres --args "postgres://user:password@localhost/dbname"
claude "현재 users 테이블의 스키마를 보여줘"
```

## 이 단계에서 중요한 판단 기준

Claude Code에 작업을 위임할 때는 작업을 작게 나눌수록 결과의 정확도가 높다 — "전체 프로젝트를 리팩토링해줘"보다 "auth 모듈의 이 함수만 리팩토링해줘"가 훨씬 믿을 만한 결과를 낸다.

## 한 줄 요약 — 이것만 기억하면 된다

**Claude Code는 탐색과 반복 작업을 위임하는 도구다 — 판단과 검증은 개발자가 직접 해야 한다.**

## 나중에 더 깊게 들어가면

- CLAUDE.md에 팀 규칙과 커스텀 도구를 등록하는 구체적인 방법
- MCP 서버를 직접 작성해 내부 시스템과 연결하기
- Git worktree와 Claude Code를 조합한 병렬 개발 워크플로우

---

**원본:** [Claude Code로 개발 생산성 극대화하기: 실전 활용 가이드](https://memoryhub.tistory.com/680)
