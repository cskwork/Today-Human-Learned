# Model Context Protocol (MCP) — 나만의 AI 도구 서버 만들기

> **TL;DR**
> MCP는 AI 앱이 파일, DB, API 같은 외부 시스템과 통신하는 표준 프로토콜이며, Python 몇 십 줄로 나만의 도구 서버를 만들 수 있다.

---

## MCP를 왜 쓰는지 감 잡기

AI 모델에 새로운 도구를 연결할 때마다 커스텀 통합 코드를 따로 작성하면, M개의 AI 앱과 N개의 도구를 연결하는 데 M x N개의 통합이 필요하다. 유지보수 비용이 폭발적으로 늘어난다.

MCP(Model Context Protocol)는 Anthropic이 2024년 11월 오픈소스로 공개한 표준 프로토콜이다. USB-C처럼 하나의 규격을 정의해서, MCP를 지원하는 AI 앱은 MCP 서버라면 어디든 연결할 수 있다. M+N개의 작업으로 줄어든다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: AI 앱(호스트) → MCP 클라이언트 → MCP 서버 → 외부 시스템(파일/DB/API)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| MCP 호스트 | MCP 클라이언트를 내장한 AI 앱. Claude Desktop, VS Code, Cursor 등 |
| MCP 서버 | 특정 기능을 MCP 규격으로 노출하는 경량 프로그램. 직접 만들 수 있다 |
| Tool | AI가 호출해서 실행할 수 있는 함수. `@mcp.tool()` 데코레이터로 등록 |
| Resource | AI가 읽을 수 있는 데이터 소스. 파일 내용, API 응답, DB 레코드 등 |
| Prompt | 사전 정의된 지시문 템플릿. 반복적인 작업 지시를 표준화할 때 사용 |

## 예를 들어 설명하면

Python으로 계산기 MCP 서버를 만드는 최소 예제다.

```python
from mcp.server.fastmcp import FastMCP
import math

mcp = FastMCP("계산기 서버")

@mcp.tool()
def add(a: float, b: float) -> float:
    """두 숫자를 더합니다"""
    return a + b

@mcp.tool()
def sqrt(number: float) -> float:
    """숫자의 제곱근을 계산합니다"""
    if number < 0:
        raise ValueError("음수의 제곱근은 계산할 수 없습니다")
    return math.sqrt(number)

if __name__ == "__main__":
    mcp.run()
```

서버를 Claude Desktop에 연결하려면 설정 파일에 아래를 추가한다.

```json
{
  "mcpServers": {
    "calculator": {
      "command": "python",
      "args": ["/path/to/calculator_server.py"]
    }
  }
}
```

이후 Claude Desktop을 재시작하면 `add`, `sqrt` 도구를 바로 쓸 수 있다.

## 이 단계에서 중요한 판단 기준

MCP 서버는 시스템에 직접 접근할 수 있으므로, API 키 등 민감 정보는 반드시 환경 변수로 관리하고 입력값 검증을 반드시 구현하라.

## 한 줄 요약 — 이것만 기억하면 된다

**MCP 서버는 `@mcp.tool()` 데코레이터로 함수를 등록하고 설정 파일에 경로를 추가하는 것만으로 AI 앱에 나만의 도구를 연결할 수 있다.**

## 나중에 더 깊게 들어가면

- TypeScript SDK로 MCP 서버 구현하기
- 비동기 Tool로 외부 API 호출 처리하기 (`async def`)
- MCP Inspector로 서버 동작 디버깅하기 (`mcp dev server.py`)

---

**원본:** [Model Context Protocol (MCP) - 나만의 AI 도구 서버 만들기 완전 정복](https://memoryhub.tistory.com/627)
