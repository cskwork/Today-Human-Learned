# Bruno MCP Server — LLM에게 API 테스트 능력을 붙이는 어댑터

> **TL;DR**
> Bruno MCP Server는 AI 에이전트가 Bruno CLI를 통해 API 컬렉션을 실행하거나 테스트 코드를 생성할 수 있도록 중간에서 연결해주는 얇은 어댑터다.

---

## Bruno MCP Server를 왜 쓰는지 감 잡기

Bruno는 오픈소스 HTTP API 클라이언트이자 테스트 러너다. 그런데 AI 에이전트(Claude 등)는 Bruno를 직접 호출할 방법이 없다. 에이전트는 MCP(Model Context Protocol)라는 표준 통신 방식만 이해하기 때문이다.

Bruno MCP Server는 그 사이를 잇는 다리다. 에이전트가 MCP로 명령을 보내면, 서버가 이를 Bruno CLI 명령으로 바꿔 실행하고 결과를 다시 에이전트에 돌려준다. 이 과정에서 LLM은 셸을 직접 건드리지 않고, Bruno CLI는 MCP를 파싱하지 않는다. 각 계층이 자기 역할만 한다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: LLM 명령 → MCP(JSON-RPC) → Bruno MCP Server → Bruno CLI → 결과 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| MCP (Model Context Protocol) | LLM이 외부 도구를 호출할 때 쓰는 표준 통신 규약 (JSON-RPC 기반) |
| Bruno CLI | 터미널에서 API 컬렉션을 실행하고 결과를 JSON으로 출력하는 커맨드라인 도구 |
| Tool | LLM에게 노출되는 호출 가능한 기능 단위 (예: `run-collection`) |
| Zod | 런타임에 데이터 구조를 검증하는 TypeScript 라이브러리 — 잘못된 입력을 즉시 차단 |
| stdio 전송 | 프로세스 간 표준 입출력(stdin/stdout)으로 MCP 메시지를 주고받는 방식 |

## 예를 들어 설명하면

Claude에게 "users API 컬렉션을 돌려줘"라고 요청하면 내부에서 이런 흐름이 일어난다.

```
Claude ──ListTools──────────────> Server   (어떤 도구가 있니?)
Claude <──available tools─────────          (run-collection, writeTestScript 등)
Claude ──CallTool: run-collection─> Server ─Zod 검증─> Runner
Runner ─spawn───────────────────> Bruno CLI
Bruno CLI ─results.json──────────> Runner ─파싱──> Server
Server ─MCP 응답──────────────────> Claude
```

각 단계에서 오류가 발생하면 MCP 표준 오류 객체로 감싸져 Claude에게 전달된다. Claude는 오류 메시지를 읽고 재시도 여부를 판단한다.

## 이 단계에서 중요한 판단 기준

새 기능을 추가할 때는 `runner.ts`에 메서드를 구현하고 `server.ts`에 스키마만 등록하면 된다 — 진입점이나 전송 계층은 건드리지 않아도 된다.

## 한 줄 요약 — 이것만 기억하면 된다

**Bruno MCP Server는 LLM과 Bruno CLI 사이의 타입 안전 어댑터다 — LLM은 MCP만, Bruno CLI는 셸만 알면 된다.**

## 나중에 더 깊게 들어가면

- gRPC 전송으로 stdio 대신 네트워크 기반 통신 전환
- MCP 청크 메시지를 이용한 장시간 컬렉션 실행 스트리밍
- Bruno JUnit 출력을 CI 대시보드로 직접 포워딩하는 플러그인 방식

---

**원본:** [Bruno MCP Server System Design](https://memoryhub.tistory.com/677)
