+++
title = "Claude Code — 터미널에서 코드베이스 전체를 다루는 AI 에이전트"
date = "2025-06-07"
description = "Claude Code는 터미널에 상주하는 AI 코딩 에이전트다. 자연어 명령 하나로 코드 탐색, 수정, 테스트 실행, 커밋, PR 생성까지 처리한다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Claude Code는 터미널에 상주하는 AI 코딩 에이전트다. 자연어 명령 하나로 코드 탐색, 수정, 테스트 실행, 커밋, PR 생성까지 처리한다.

---

## Claude Code를 왜 쓰는지 감 잡기

기존 AI 코딩 도구 대부분은 별도 채팅 창 형태였다. 개발자가 코드 일부를 복사해서 붙여넣고 맥락을 직접 설명해야 했기 때문에 작업 흐름이 자주 끊겼다.

Claude Code는 터미널에 직접 통합된다. "에이전트 검색" 방식으로 프로젝트 전체 구조와 의존성을 스스로 파악하기 때문에, 개발자가 파일을 지정하지 않아도 된다. 파일 수정, 테스트 실행, 커밋, PR 생성을 하나의 세션 안에서 연속으로 처리한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 자연어 명령 → 코드베이스 자율 탐색 → 코드 수정 → 테스트 → 커밋/PR`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 에이전트 검색 (Agentic Search) | Claude가 명령을 받으면 스스로 관련 파일과 의존성을 찾아다니는 탐색 방식 |
| CLAUDE.md | Claude가 세션 시작 시 자동으로 읽는 파일. 프로젝트 규칙과 자주 쓰는 명령어를 여기에 정의한다 |
| 헤드리스 모드 | `-p` 플래그로 Claude를 스크립트나 CI/CD 파이프라인에 자동화해서 쓰는 방식 |
| YOLO 모드 | `--dangerously-skip-permissions` 옵션으로 모든 권한 확인을 건너뛰는 모드. 격리된 환경에서만 권장한다 |
| MCP (Model Context Protocol) | Claude가 Puppeteer, Sentry 같은 외부 도구 서버와 연동할 때 쓰는 표준 프로토콜 |

## 예를 들어 설명하면

복잡한 기능을 맡길 때 가장 효과적인 4단계 워크플로우다.

```bash
# 1. 탐색: 코드를 읽되 아직 수정하지 않는다
claude "read the file that handles logging, but don't write any code yet."

# 2. 계획: think hard 키워드로 더 깊은 추론을 유도한다
claude "think hard and make a plan to improve the logging logic."

# 3. 구현: 계획대로 작성하고 검증한다
claude "implement the solution according to your plan. verify as you go."

# 4. 커밋: 변경 사항을 마무리한다
claude "commit the result and create a pull request. update the README too."
```

고급 기능 요약은 아래 표로 정리했다.

| 기능 | 설명 |
|---|---|
| 스크린샷 기반 개발 | 디자인 이미지를 보고 UI를 코드로 구현, 스크린샷으로 결과 비교 반복 |
| 헤드리스 모드 | `-p` 플래그로 CI 스크립트에 통합, 대규모 마이그레이션 자동화 |
| MCP 연동 | `.mcp.json`으로 외부 도구 서버를 프로젝트에 추가 |

## 이 단계에서 중요한 판단 기준

"think", "think hard", "think harder" 같은 키워드를 프롬프트에 포함시키면 Claude가 대안을 더 신중하게 평가한다. 복잡한 리팩토링이나 설계 변경처럼 실수 비용이 큰 작업에 특히 유효하다.

## 한 줄 요약 — 이것만 기억하면 된다

**CLAUDE.md에 프로젝트 컨텍스트를 준비하고, 탐색 → 계획 → 구현 → 커밋 순서로 Claude Code에게 작업을 위임하면 전체 개발 사이클을 자동화할 수 있다.**

## 나중에 더 깊게 들어가면

- `.mcp.json`으로 Puppeteer MCP 서버를 프로젝트에 추가하고 시각적 테스트 자동화하기
- git worktree와 병렬 Claude 인스턴스를 결합한 다중 에이전트 워크플로우
- YOLO 모드를 Docker 격리 환경에서 안전하게 활용하는 CI 파이프라인 설계

---

**원본:** [클로드 코드(Claude Code) - 터미널에서 일하는 AI 동료 개발자 — https://memoryhub.tistory.com/664](https://memoryhub.tistory.com/664)
