+++
title = "Claude Agent SDK로 프로덕션 수준의 에이전트 구축하기"
date = "2025-10-25"
description = "프로덕션 에이전트는 오류 처리, 권한 제어, 훅 기반 안전장치, 관찰 가능성을 갖춰야 하며, 이 네 가지가 없으면 프로토타입에 머문다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> 프로덕션 에이전트는 오류 처리, 권한 제어, 훅 기반 안전장치, 관찰 가능성을 갖춰야 하며, 이 네 가지가 없으면 프로토타입에 머문다.

---

## 프로덕션 에이전트를 왜 따로 배워야 하는지 감 잡기

개발 환경에서 동작하는 에이전트와 프로덕션에서 안정적으로 실행되는 에이전트는 다르다. 연결 오류가 나도 재시도하고, 위험한 명령은 실행 전에 차단하고, 에이전트가 오작동하면 어디서 무엇이 잘못됐는지 추적할 수 있어야 한다. Claude Agent SDK는 이 모든 기능을 내장하고 있지만, 제대로 설정하지 않으면 작동하지 않는다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: 오류 계층 처리 → 훅으로 안전장치 → 권한 최소화 → 스트리밍 응답 → 관찰 가능성`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 훅 (Hook) | 에이전트가 도구를 실행하기 직전(PreToolUse)이나 직후(PostToolUse)에 끼어드는 검증 함수 |
| 인프로세스 MCP 서버 | 외부 프로세스 없이 같은 프로세스 안에서 실행되는 커스텀 도구 서버; 가장 빠름 |
| 컨텍스트 격리 | 서브에이전트가 서로의 컨텍스트를 볼 수 없게 분리하는 원칙; 정보 유출 방지 |
| 오케스트레이터 패턴 | 중앙 에이전트가 전문화된 서브에이전트에게 작업을 분배하는 멀티 에이전트 구조 |
| CLAUDE.md | 프로젝트 규칙과 컨텍스트를 세션 간에 영속적으로 저장하는 메모리 파일 |

## 예를 들어 설명하면

위험한 Bash 명령을 사전 차단하는 PreToolUse 훅이다.

```python
import re
from claude_agent_sdk import ClaudeAgentOptions, HookMatcher

def validate_bash_commands(event):
    dangerous = [r'rm\s+-rf', r'sudo', r'curl.*\|\s*sh', r'mkfs']
    command = event.tool_input.get('command', '')
    for pattern in dangerous:
        if re.search(pattern, command):
            return {"block": True, "message": f"차단된 명령: {pattern}"}
    return {"allow": True}

options = ClaudeAgentOptions(
    hooks=[{
        "matcher": HookMatcher(tool_name="bash"),
        "pre_tool_use": validate_bash_commands
    }]
)
```

이 패턴은 에이전트가 `rm -rf`나 `curl | sh` 같은 명령을 실행하려 할 때 실행 전에 막는다. 훅이 없으면 에이전트가 의도치 않게 시스템을 손상시킬 수 있다.

## 이 단계에서 중요한 판단 기준

서브에이전트별로 `allowed_tools`를 명시적으로 제한하라. `acceptAll` 권한 모드는 프로덕션에서 절대 쓰지 않는다.

## 한 줄 요약 — 이것만 기억하면 된다

**훅으로 위험한 명령을 차단하고, 권한을 최소화하고, 스트리밍과 추적을 켜야 프로덕션 에이전트라고 부를 수 있다.**

## 나중에 더 깊게 들어가면

- OpenTelemetry로 에이전트 요청 전 구간 추적하기
- 비용 최적화를 위한 컨텍스트 관리 전략 (선택적 컨텍스트 로딩, 주기적 정리)
- 테스트 우선(test-first) 멀티 에이전트 파이프라인 설계

---

**원본:** [Claude Agent SDK로 프로덕션 수준의 에이전트 구축하기](https://memoryhub.tistory.com/874)
