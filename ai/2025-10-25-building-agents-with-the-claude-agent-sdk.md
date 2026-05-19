# Claude Agent SDK로 에이전트 만들기

> **TL;DR**
> Claude Agent SDK는 파일 시스템, 터미널, 웹을 다룰 수 있는 자율 에이전트를 Python 또는 TypeScript로 구축하는 Anthropic 공식 프레임워크다.

---

## Claude Agent SDK를 왜 쓰는지 감 잡기

AI 모델은 원래 텍스트를 입력받아 텍스트를 돌려주는 방식으로만 동작했다. 그런데 실제 개발 업무에는 파일을 읽고, 코드를 실행하고, 테스트 결과를 확인하는 순환 작업이 필요하다. Claude Agent SDK는 Claude 모델에 이 순환 작업 능력을 붙여준다. "Claude에게 컴퓨터를 한 대 쥐여 준다"는 표현이 이 철학을 잘 요약한다.

코딩 자동화, 장애 대응 스크립트 작성, 데이터 분석 리포트 생성 등 반복적이고 다단계인 작업이 주요 대상이다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: 컨텍스트 수집 → 행동 수행 → 결과 검증 → 반복`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 에이전트 루프 | 에이전트가 작업을 완료할 때까지 수집 → 행동 → 검증을 반복하는 사이클 |
| `query()` | 도구 없이 텍스트만 생성하는 단순 호출 함수; 가볍고 빠름 |
| `ClaudeAgentClient` | 도구, 권한, 세션을 관리하는 핵심 클래스; 풀 에이전트를 만들 때 사용 |
| `permission_mode` | 에이전트가 행동할 때 사람의 승인이 필요한지 결정하는 설정값 |
| MCP (Model Context Protocol) | 에이전트에게 커스텀 함수나 외부 API를 도구로 등록하는 표준 프로토콜 |

## 예를 들어 설명하면

파일을 만드는 기본 에이전트 코드다.

```python
import anyio
from claude_agent_sdk import ClaudeAgentClient, ClaudeAgentOptions

async def main():
    client = ClaudeAgentClient()
    options = ClaudeAgentOptions(
        system_prompt="파일 작업을 수행하는 어시스턴트입니다.",
        permission_mode="manual",       # 매 행동마다 사람 승인 필요
        allowed_tools=["Read", "Write", "Grep"],
        max_turns=10
    )
    async for message in client.query_agent(
        prompt="hello.txt 파일에 'Hello, Agent!'를 작성해줘",
        options=options
    ):
        print(message)

anyio.run(main)
```

`permission_mode`를 `"acceptAll"`로 바꾸면 완전 자율 모드가 되지만, 프로덕션에서는 `"manual"` 또는 `"acceptEdits"`를 권장한다.

## 이 단계에서 중요한 판단 기준

단순 텍스트 생성이면 `query()`로 충분하고, 파일·터미널·외부 API를 다루는 자율 작업이면 `ClaudeAgentClient`를 써야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**에이전트 루프(수집 → 행동 → 검증 → 반복)와 권한 모드 설정이 Claude Agent SDK의 핵심이다.**

## 나중에 더 깊게 들어가면

- MCP로 커스텀 도구를 등록하는 방법
- 컨텍스트 자동 압축(compaction)과 장시간 세션 관리
- 멀티 에이전트 오케스트레이터 패턴

---

**원본:** [Building Agents with the Claude Agent SDK](https://memoryhub.tistory.com/872)
