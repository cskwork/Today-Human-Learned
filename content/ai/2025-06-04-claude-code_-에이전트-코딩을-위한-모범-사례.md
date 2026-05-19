+++
title = "Claude Code 에이전트 코딩 모범 사례"
date = "2025-06-04"
description = "Claude Code를 잘 쓰려면 CLAUDE.md로 컨텍스트를 미리 준비하고, 워크플로우는 \"탐색 → 계획 → 구현 → 커밋\" 순서를 따르면 된다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Claude Code를 잘 쓰려면 CLAUDE.md로 컨텍스트를 미리 준비하고, 워크플로우는 "탐색 → 계획 → 구현 → 커밋" 순서를 따르면 된다.

---

## Claude Code를 왜 쓰는지 감 잡기

Claude Code는 Anthropic이 만든 CLI 기반 AI 코딩 에이전트다. 터미널에서 자연어로 명령을 내리면 파일을 읽고, 코드를 작성하고, 테스트를 실행하며, Git 커밋까지 처리한다.

기존 챗봇형 도구와 다른 점은 프로젝트 전체 구조를 스스로 탐색한다는 것이다. 개발자가 파일을 일일이 붙여넣지 않아도 코드베이스의 의존성과 흐름을 파악하고 작업한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 자연어 명령 → 코드베이스 탐색 → 코드 작성/수정 → 테스트 실행 → 커밋`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| CLAUDE.md | 세션 시작 시 Claude가 자동으로 읽는 프로젝트 설명 파일. 자주 쓰는 명령어, 스타일 규칙을 여기에 적어둔다 |
| MCP (Model Context Protocol) | Claude Code가 Puppeteer, Sentry 같은 외부 도구 서버와 연동할 때 쓰는 표준 프로토콜 |
| 헤드리스 모드 | `-p` 플래그를 써서 CI/CD나 스크립트에서 Claude를 자동으로 실행하는 방식 |
| 허용 도구 목록 | Claude가 권한 요청 없이 실행할 수 있는 도구 목록. `.claude/settings.json`에 정의한다 |
| git worktree | 동일한 저장소에서 여러 브랜치를 동시에 다른 폴더에 체크아웃해 병렬 작업하는 Git 기능 |

## 예를 들어 설명하면

복잡한 기능 구현 시 가장 효과적인 워크플로우는 아래 4단계다.

```
# 1. 탐색: 먼저 읽고 파악만 한다
claude "logging.py와 관련 파일을 읽어봐. 코드는 아직 쓰지 마."

# 2. 계획: 깊이 생각하게 유도한다
claude "think hard — 로깅 로직 개선 계획을 세워줘."

# 3. 구현: 계획대로 작성한다
claude "계획대로 구현해줘. 진행하면서 검증도 해줘."

# 4. 커밋: 마무리한다
claude "결과를 커밋하고 PR을 생성해줘. README도 업데이트해줘."
```

`think`, `think hard` 같은 키워드를 프롬프트에 포함하면 Claude가 더 신중하게 대안을 검토하고 계획을 세운다.

## 이 단계에서 중요한 판단 기준

Claude Code에서 성능 차이를 만드는 가장 중요한 요소는 프롬프트의 구체성이다. "foo.py에 테스트 추가"보다 "로그아웃된 사용자의 엣지 케이스를 다루는 테스트를 foo.py에 추가해줘. mock은 쓰지 마"처럼 제약 조건을 명시할수록 결과가 좋아진다.

## 한 줄 요약 — 이것만 기억하면 된다

**CLAUDE.md에 프로젝트 컨텍스트를 정리해두고, 탐색 → 계획 → 구현 → 커밋 순서를 지키면 Claude Code의 성능이 확실히 달라진다.**

## 나중에 더 깊게 들어가면

- `.mcp.json`으로 Puppeteer, Sentry 같은 MCP 서버를 프로젝트에 추가하기
- `--dangerously-skip-permissions` 옵션을 Docker 격리 환경에서 안전하게 활용하기
- 여러 Claude 인스턴스를 git worktree와 함께 병렬로 실행하는 다중 에이전트 워크플로우

---

**원본:** [Claude Code: 에이전트 코딩을 위한 모범 사례 — https://memoryhub.tistory.com/648](https://memoryhub.tistory.com/648)
