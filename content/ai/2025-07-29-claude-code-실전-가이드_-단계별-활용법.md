+++
title = "Claude Code 실전 가이드 — 초보부터 고급까지"
date = "2025-07-29"
description = "Claude Code는 터미널에서 실행하는 AI 코딩 도우미로, CLAUDE.md로 맥락을 주고 Plan 모드로 안전하게 분석한 뒤 실행하는 흐름이 핵심이다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Claude Code는 터미널에서 실행하는 AI 코딩 도우미로, CLAUDE.md로 맥락을 주고 Plan 모드로 안전하게 분석한 뒤 실행하는 흐름이 핵심이다.

---

## Claude Code를 왜 쓰는지 감 잡기

코드베이스가 커질수록 AI에게 "이 파일 수정해줘"만 하면 엉뚱한 결과가 나온다. Claude Code는 프로젝트 전체를 읽고 맥락을 파악한 뒤 작업하는 터미널 기반 도구다. 설치는 `npm install -g @anthropic-ai/claude-code`이고, 프로젝트 디렉토리에서 `claude`를 실행한다.

작업이 잘못된 방향으로 가면 ESC로 언제든 멈출 수 있다. 중요한 작업 전에는 반드시 백업한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: CLAUDE.md로 맥락 제공 → Plan 모드로 계획 수립 → 실행 → ESC로 중단 가능`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| CLAUDE.md | 프로젝트 루트에 두는 지침 파일. Claude가 이 파일을 읽고 프로젝트 규칙을 따른다 |
| Plan 모드 | 실제로 파일을 건드리지 않고 분석과 계획만 하는 읽기 전용 모드 |
| settings.json | 어떤 명령어를 허용하고 차단할지 정의하는 보안 설정 파일 |
| Commands | `.claude/commands/` 폴더에 저장한 재사용 프롬프트. `/명령어`로 즉시 호출 |
| Subagents | 메인 컨텍스트를 오염시키지 않고 독립적으로 작업을 처리하는 하위 에이전트 |

## 예를 들어 설명하면

실제 프로젝트에서 사용하는 `settings.json` 예시다. 위험한 명령어는 차단하고, 안전한 명령어만 허용한다.

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run lint)",
      "Bash(npm run test:*)",
      "Bash(git log:*)",
      "Read",
      "Grep",
      "WebSearch"
    ],
    "deny": [
      "Bash(rm:*)",
      "Read(**/application.yml)"
    ],
    "defaultMode": "plan"
  }
}
```

`defaultMode: "plan"`으로 설정하면 기본적으로 Plan 모드로 시작해 실수를 줄일 수 있다.

자주 쓰는 작업은 Commands로 저장해둔다.

```bash
mkdir -p ~/.claude/commands
# 기능 추가 계획 수립 커맨드 저장
echo "Ultra Think. We would like to add [FEATURE]..." > ~/.claude/commands/add-feature.md
```

이후 Claude Code 안에서 `/add-feature`를 입력하면 즉시 호출된다.

## 이 단계에서 중요한 판단 기준

새 기능을 추가하기 전에 항상 Plan 모드로 변경 범위를 먼저 확인한다 — 나중에 ESC로 되돌리는 것보다 빠르다.

## 한 줄 요약 — 이것만 기억하면 된다

**CLAUDE.md로 맥락을 주고, Plan 모드로 계획을 검토한 뒤 실행하면 Claude Code 실수의 대부분을 막을 수 있다.**

## 나중에 더 깊게 들어가면

- MCP 연동으로 Context7, DeepWiki 같은 외부 도구 불러오기
- Subagents로 복잡한 작업을 독립 에이전트에 위임하는 방법
- `--dangerously-skip-permissions`(YOLO 모드)를 격리 환경에서만 안전하게 쓰는 조건

---

**원본:** [Claude Code 실전 가이드: 단계별 활용법 — https://memoryhub.tistory.com/733](https://memoryhub.tistory.com/733)
