# Gemini CLI 보안 설정: 위험 명령어 차단부터 MCP 서버 구성까지

> **TL;DR**
> Gemini CLI 기본값은 샌드박스가 없는 상태다. coreTools로 허용 명령을 제한하고, Docker 샌드박스로 실행 환경을 격리해야 안전하게 쓸 수 있다.

---

## Gemini CLI 보안을 왜 신경 써야 하는지 감 잡기

AI 코딩 도구가 터미널에서 `rm -rf`를 실행하면 어떻게 될까. 2025년 6월 출시 직후 발견된 Gemini CLI 취약점은 이 가능성을 현실로 보여줬다. 악성 README 파일에 숨겨진 프롬프트 인젝션이 화이트리스트를 우회해, 사용자가 코드를 분석하는 동안 환경 변수가 외부 서버로 유출됐다.

Google은 이를 최고 심각도(P1/S1)로 분류하고 v0.1.14에서 패치했다. 그러나 핵심 교훈은 남는다. Gemini CLI의 기본 설정은 "no sandbox" 모드다. 화면 하단에 빨간 경고가 표시되지만 많은 개발자가 무시한다. 보안 설정은 선택이 아니라 필수다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: coreTools(허용 명령 제한) → 샌드박스(실행 환경 격리) → MCP 서버 권한 제어`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| coreTools | AI가 사용할 수 있는 도구를 화이트리스트로 지정하는 설정. 목록에 없으면 실행 불가 |
| excludeTools | 특정 도구만 차단하는 블랙리스트 방식. 우회 가능성이 있어 coreTools보다 덜 안전하다 |
| 샌드박스 | AI 명령을 격리된 환경(Docker, macOS Seatbelt 등)에서 실행해 시스템 손상을 막는 기술 |
| MCP | AI가 GitHub, DB 같은 외부 시스템과 연동할 때 쓰는 프로토콜 (Model Context Protocol) |
| 프롬프트 인젝션 | 악의적 텍스트(README 등)에 숨긴 명령이 AI를 통해 실행되는 공격 방식 |

## 예를 들어 설명하면

`~/.gemini/settings.json`에 아래 설정을 추가하면 읽기와 Git 조회만 허용하고 삭제/다운로드 명령은 원천 차단된다.

```json
{
  "tools": {
    "sandbox": "docker",
    "core": [
      "ReadFileTool",
      "GlobTool",
      "ShellTool(ls)",
      "ShellTool(grep)",
      "ShellTool(git)"
    ],
    "exclude": [
      "ShellTool(rm -rf)",
      "ShellTool(curl)",
      "ShellTool(wget)"
    ]
  }
}
```

프로젝트별로 DB MCP 서버를 추가할 때는 `.gemini/settings.json`(프로젝트 루트)을 별도로 만들고 `includeTools`로 조회 기능만 허용한다.

```json
{
  "mcpServers": {
    "project-db": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": { "DATABASE_URL": "$DATABASE_URL" },
      "includeTools": ["query", "list-tables"],
      "excludeTools": ["execute", "drop-table"]
    }
  }
}
```

설정 파일은 4단계 우선순위로 병합된다. 프로젝트 설정은 사용자 전역 설정에 더해지므로, 전역에 기본 보안 정책을 두고 프로젝트마다 필요한 MCP 서버를 추가하는 구조가 권장된다.

## 이 단계에서 중요한 판단 기준

excludeTools는 문자열 기반 차단이라 `rm -r -f` 같은 변형 명령을 막지 못한다. 팀 협업이나 외부 코드 분석 시에는 반드시 coreTools 화이트리스트 방식과 Docker 샌드박스를 함께 써야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Gemini CLI는 기본이 무방비 상태이므로, coreTools 화이트리스트와 Docker 샌드박스를 직접 설정해야 안전하게 쓸 수 있다.**

## 나중에 더 깊게 들어가면

- macOS Seatbelt 프로파일별 차이(permissive-open vs restrictive-closed)
- YOLO 모드와 샌드박스 자동 활성화의 관계
- 엔터프라이즈 환경에서 시스템 레벨 MCP 허용 목록 강제 적용

---

**원본:** [Gemini CLI 보안 설정 완벽 가이드: 위험 명령어 차단부터 MCP 서버 구성](https://memoryhub.tistory.com/934)
