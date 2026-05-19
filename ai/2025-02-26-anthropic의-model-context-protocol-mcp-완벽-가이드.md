# Model Context Protocol(MCP) — AI에게 손발을 달아주는 방법

> **TL;DR**
> MCP는 Claude 같은 AI 모델이 파일, 데이터베이스, 외부 서비스에 표준화된 방식으로 접근할 수 있게 해주는 프로토콜이다.

---

## MCP를 왜 쓰는지 감 잡기

AI 모델은 기본적으로 텍스트 입력을 받아 텍스트를 출력하는 시스템이다. 사용자가 "내 프로젝트 폴더의 버그를 찾아줘"라고 요청해도 모델 자체는 파일에 접근할 수 없다. MCP는 이 한계를 해결하기 위해 만들어졌다. AI 모델과 외부 세계 사이에 표준 연결 방식을 정의해서, 모델이 파일을 읽고, 명령을 실행하고, API를 호출할 수 있게 한다. USB 규격처럼, 어떤 장치든 같은 방식으로 연결할 수 있는 표준이다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 사용자 요청 → Claude → MCP 클라이언트 → MCP 서버 → 외부 리소스 → 결과 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| MCP 서버 | 특정 외부 리소스(파일, DB, GitHub 등)에 접근하는 기능을 제공하는 프로세스 |
| MCP 클라이언트 | Claude가 MCP 서버와 통신하기 위해 내부적으로 쓰는 연결 역할 |
| 도구(Tool) | MCP 서버가 AI에게 제공하는 실행 가능한 기능 단위 |
| 프로토콜(Protocol) | 서로 다른 시스템이 정해진 방식으로 소통하기 위한 규칙 모음 |
| 스코프(Scope) | MCP 서버가 접근할 수 있는 범위. 예: 특정 폴더, 특정 저장소만 허용 |

## 예를 들어 설명하면

Claude Desktop에서 파일 시스템 MCP와 GitHub MCP를 함께 쓰는 설정은 이렇다.

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/me/projects"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_..."
      }
    }
  }
}
```

이 설정 후에는 "내 프로젝트에서 인증 관련 코드를 찾아 보안 취약점을 확인해줘"라고 요청하면 Claude가 실제 파일을 읽고 GitHub 이슈를 참조해서 답할 수 있다.

자주 쓰이는 공식 MCP 서버 목록은 다음과 같다.

| 서버 | 용도 |
|---|---|
| server-filesystem | 로컬 파일 읽기/쓰기 |
| server-github | GitHub 저장소, 이슈, PR 관리 |
| server-postgres | PostgreSQL 쿼리 및 스키마 탐색 |
| server-fetch | 웹 콘텐츠 가져오기 |
| server-memory | 대화 간 영구 메모리 저장 |

## 이 단계에서 중요한 판단 기준

MCP 서버는 로컬 시스템에 실제 접근하므로, 신뢰할 수 없는 출처의 서버를 추가하거나 API 키를 설정 파일에 평문으로 저장하면 보안 사고로 이어진다. 허용 경로와 환경 변수 관리를 시작 전에 먼저 정해야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**MCP는 AI 모델이 파일, DB, 외부 서비스에 안전하게 연결되도록 표준화된 인터페이스를 제공하는 프로토콜이다.**

## 나중에 더 깊게 들어가면

- 커스텀 MCP 서버 만들기 — TypeScript SDK로 자신만의 도구를 정의하는 방법
- MCP와 함수 호출(Function Calling)의 차이 — 언제 MCP를 쓰고 언제 일반 도구 호출을 쓰는가
- MCP 보안 모델 — 샌드박스 격리와 권한 범위 제한을 어떻게 설계하는가

---

**원본:** [Anthropic의 Model Context Protocol(MCP) 완벽 가이드 — https://memoryhub.tistory.com/456]
