# MCP(Model Context Protocol): AI와 데이터를 연결하는 새로운 표준

> **TL;DR**
> MCP는 AI 모델이 외부 데이터와 도구에 접근하는 방식을 표준화한 오픈 프로토콜이다. 각 시스템마다 따로 연결 코드를 짜던 시대를 끝낸다.

---

## MCP를 왜 쓰는지 감 잡기

기존에 AI 모델이 GitHub 코드나 사내 데이터베이스를 읽으려면, 매번 그 시스템 전용 연결 코드를 새로 짜야 했다. N개의 데이터 소스가 있으면 N개의 커스텀 통합이 필요한 구조다. Anthropic이 2024년 11월 오픈소스로 공개한 MCP는 이 문제를 해결한다. 마치 USB-C 포트처럼, 표준 인터페이스 하나로 모든 주변 장치(데이터 소스·도구)를 연결한다. 한 번 만든 MCP 서버는 Claude뿐 아니라 MCP를 지원하는 모든 AI 모델에서 동작한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: AI 클라이언트 → MCP 프로토콜 → MCP 서버 → 데이터/도구`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| MCP Server | 데이터나 기능을 AI에게 제공하는 경량 프로그램 (도구 판매점) |
| MCP Client | AI 모델이 서버와 통신하는 앱 쪽 구현체 (도구를 요청하는 손님) |
| Resources | 서버가 읽기 전용으로 노출하는 데이터 (파일·DB 조회 등) |
| Tools | 서버가 AI에게 제공하는 실행 가능한 기능 (함수 호출 단위) |
| Prompts | 서버가 미리 정의해 둔 재사용 가능한 지시 템플릿 |

## 예를 들어 설명하면

날씨 정보를 조회하는 MCP 서버를 TypeScript로 최소 구현하면 이렇다.

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new McpServer({ name: "weather-mcp", version: "1.0.0" });

server.tool(
  "get_weather",
  { description: "도시의 날씨를 반환한다", inputSchema: { type: "object", properties: { city: { type: "string" } } } },
  async ({ city }) => ({ content: [{ type: "text", text: `${city}: 맑음 22°C` }] })
);

await server.connect(new StdioServerTransport());
```

Claude Desktop의 설정 파일(`~/Library/Application Support/Claude/claude_desktop_config.json`)에 서버 경로를 등록하면 Claude가 이 도구를 직접 호출한다.

## 이 단계에서 중요한 판단 기준

MCP 서버를 설계할 때 도구 하나는 기능 하나만 담당해야 한다. 하나의 도구에 여러 역할을 몰아넣으면 AI가 언제 어떤 도구를 써야 할지 판단하지 못한다.

## 한 줄 요약 — 이것만 기억하면 된다

**MCP는 AI와 외부 세계를 잇는 표준 연결 포트다. 한 번 만들면 어느 AI에도 꽂힌다.**

## 나중에 더 깊게 들어가면

- MCP 전송 방식 비교: stdio vs SSE(HTTP)
- 입력 스키마 검증을 위한 Zod 활용
- Human-in-the-loop 패턴: 민감한 작업에 사용자 승인 요구하기

---

**원본:** [MCP(Model Context Protocol) 완벽 정복: AI와 데이터를 연결하는 새로운 표준!](https://memoryhub.tistory.com/696)
