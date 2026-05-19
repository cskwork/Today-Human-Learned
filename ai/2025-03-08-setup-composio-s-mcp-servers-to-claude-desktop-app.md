# Composio MCP 서버를 Claude Desktop에 연결하기

> **TL;DR**
> Composio의 MCP 서버를 `claude_desktop_config.json`에 등록하면 Claude가 Gmail, Notion, GitHub 등 250개 이상의 외부 서비스를 직접 다룰 수 있다.

---

## 왜 쓰는지 감 잡기

Claude Desktop은 기본적으로 대화만 한다. MCP(Model Context Protocol)는 Claude가 외부 도구를 직접 호출할 수 있는 통로다. Composio는 이 통로를 미리 만들어 놓은 서비스로, Gmail 읽기·Notion 편집·GitHub 조작 같은 작업을 OAuth 설정 없이 바로 쓸 수 있게 해준다.

핵심 흐름:

`Composio 계정 생성 → API 키 발급 → config.json 수정 → Claude 재시작`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| MCP (Model Context Protocol) | AI 모델이 외부 도구와 통신하는 방식을 정의한 규약. Anthropic이 만들었다. |
| MCP 서버 | Claude의 요청을 받아 실제 외부 서비스(Gmail 등)에 대신 전달해주는 중간 서버. |
| Composio | 250개 이상의 앱을 MCP 서버로 미리 구축해 제공하는 플랫폼. 인증도 대신 처리해준다. |
| claude_desktop_config.json | Claude Desktop이 읽는 설정 파일. MCP 서버 목록을 여기에 등록한다. |
| npx | Node.js 패키지를 설치 없이 실행하는 명령어. Composio CLI를 실행할 때 쓴다. |

## 예를 들어 설명하면

Notion MCP 서버를 연결하는 `claude_desktop_config.json` 예시다.

```json
{
  "mcpServers": {
    "composio-notion": {
      "command": "npx",
      "args": ["composio-core@rc", "mcp", "https://mcp.composio.dev/notion/<내-서버-id>", "--client", "claude"],
      "env": {
        "COMPOSIO_API_KEY": "<내-API-키>"
      }
    }
  }
}
```

설정 파일 위치:
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%appdata%\Claude\claude_desktop_config.json`

## 단계별 연결 절차

1. [composio.dev](https://composio.dev) 에서 계정 생성 후 API 키를 복사한다.
2. Node.js(LTS)가 없으면 [nodejs.org](https://nodejs.org) 에서 설치한다.
3. [mcp.composio.dev](https://mcp.composio.dev) 에서 원하는 서버(예: Notion)를 선택하고 SSE URL을 복사한다.
4. 위 JSON 예시를 `claude_desktop_config.json`에 붙여넣고 플레이스홀더를 실제 값으로 교체한다.
5. Claude Desktop을 완전히 종료 후 재시작한다.
6. 입력창 옆에 망치 아이콘이 나타나면 연결 성공이다.

## 이 단계에서 중요한 판단 기준

JSON 파일에 오타나 쉼표 오류가 하나라도 있으면 MCP 서버가 전혀 뜨지 않으므로, 저장 전에 JSON 유효성을 반드시 확인한다.

## 연결 실패 시 확인할 것

- **망치 아이콘이 없다**: JSON 문법 오류 또는 파일 경로가 틀렸다.
- **"spawn npx ENOENT" 오류**: `node`와 `npx`가 시스템 PATH에 없다. `which npx`로 경로를 확인하고 `command`에 절대경로를 쓴다.
- **Claude Developer Mode 로그**: `Menu > Help > Enable Developer Mode` 후 `Developer > Open MCP Log File`에서 오류 메시지를 확인한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Composio API 키와 서버 URL을 `claude_desktop_config.json`에 등록하고 Claude를 재시작하면, Claude가 외부 앱을 직접 조작할 수 있게 된다.**

## 나중에 더 깊게 들어가면

- 여러 MCP 서버를 동시에 등록해 Gmail + GitHub + Slack을 한 Claude 세션에서 사용하기
- Composio OAuth 인증 흐름 이해하기 (첫 사용 시 브라우저 인증 요청이 뜬다)
- `composio-core` CLI 업데이트: `npm update -g composio-core@rc`

---

**원본:** [Setup Composio's mcp servers to Claude Desktop App](https://memoryhub.tistory.com/461)
