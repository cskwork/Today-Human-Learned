# Claude Agent SDK — 자율 AI 에이전트를 실제로 만드는 법

> **TL;DR**
> Claude Agent SDK는 파일 시스템·터미널·웹을 스스로 다루는 자율 에이전트를 만드는 프레임워크이며, "수집 → 행동 → 검증" 루프를 자동 관리해 준다.

---

## Claude Agent SDK를 왜 쓰는지 감 잡기

AI에게 질문을 던지면 답을 돌려받는 것에 그치는 모델이 있다. 반면 "이 코드베이스에서 보안 취약점을 찾아 PR을 올려"처럼 여러 단계를 스스로 수행해야 하는 작업이 있다. Claude Agent SDK는 두 번째 유형을 위한 도구다.

에이전트는 세 단계를 반복한다. 먼저 파일을 읽거나 로그를 검색해 맥락을 수집한다. 그 다음 코드를 쓰거나 API를 호출하는 등 실제 행동을 한다. 마지막으로 결과를 확인해 다음 행동을 결정한다. SDK가 이 루프 전체를 관리하므로 개발자는 "무엇을 해야 하는가"에만 집중할 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 맥락 수집 → 도구 실행 → 결과 검증 → 반복`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 에이전트 루프 | 에이전트가 작업을 마칠 때까지 "수집 → 행동 → 검증"을 자동으로 반복하는 사이클 |
| 도구(Tool) | 에이전트가 실제 세계와 상호작용하는 수단 — 파일 읽기, 터미널 명령, 웹 검색 등 |
| MCP (Model Context Protocol) | 에이전트가 GitHub, Slack, DB 같은 외부 서비스와 표준 방식으로 연결하는 규격 |
| 훅(Hook) | 에이전트가 도구를 쓰기 직전·직후에 끼워 넣는 코드 — 위험한 명령 차단, 로그 기록 등에 쓴다 |
| 권한 모드 | 에이전트가 행동할 때 사람 승인이 필요한 정도 — `default`(매번 확인), `acceptEdits`(파일 쓰기만 자동), `acceptAll`(전부 자동) |

## 예를 들어 설명하면

코드 리뷰 에이전트를 만드는 시나리오다. 에이전트는 읽기 전용 도구만 허용하고, 위험한 명령은 훅으로 차단한다.

```python
async def block_dangerous_commands(input_data, tool_use_id, context):
    if input_data["tool_name"] != "Bash":
        return {}
    command = input_data["tool_input"].get("command", "")
    for pattern in ["rm -rf", "sudo", "chmod 777"]:
        if pattern in command:
            return {
                "hookSpecificOutput": {
                    "hookEventName": "PreToolUse",
                    "permissionDecision": "deny",
                    "permissionDecisionReason": f"Dangerous pattern: {pattern}"
                }
            }
    return {}

options = ClaudeAgentOptions(
    allowed_tools=["Read", "Grep", "Glob", "WebSearch"],  # 읽기 전용
    permission_mode="default",
    hooks={"PreToolUse": [HookMatcher(matcher="*", hooks=[block_dangerous_commands])]}
)
```

에이전트는 파일을 읽고 웹을 검색하면서 보안 취약점 보고서를 스스로 작성한다. 사람이 직접 파일을 열어볼 필요가 없다.

## 이 단계에서 중요한 판단 기준

프로덕션에 배포할 때는 `acceptAll`이 아닌 `default` 모드에서 시작하고, PreToolUse 훅으로 위험 명령을 명시적으로 차단한 뒤 단계적으로 권한을 완화한다.

## 한 줄 요약 — 이것만 기억하면 된다

**에이전트는 "수집 → 행동 → 검증" 루프를 반복하며, 훅과 권한 모드로 안전하게 제어한다.**

## 나중에 더 깊게 들어가면

- 오케스트레이터 패턴: 한 에이전트가 여러 전문 서브에이전트를 지휘하는 구조
- MCP 서버 직접 구현: 사내 시스템을 에이전트 도구로 연결하는 방법
- 컨텍스트 최적화: CLAUDE.md로 프로젝트 관례를 에이전트에게 전달해 토큰을 아끼는 방법

---

**원본:** [Claude Agent SDK: Production Guide for Building Valuable AI Agents](https://memoryhub.tistory.com/865)
