+++
title = "Serena MCP — AI 코딩이 갑자기 더 똑똑해지는 이유"
date = "2026-05-07"
description = "Serena는 AI 에이전트에게 IDE 수준의 심볼 검색·참조 추적·리팩터링 능력을 붙여 주는 MCP 서버다. 파일 전체 읽기와 grep 의존도를 낮춰 대형 코드베이스에서 오류와 비용을 줄인다."
tags = ["ai", "mcp", "serena", "claude-code"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Serena MCP는 AI 코딩 에이전트에게 **IDE 수준의 심볼 검색·참조 추적·리팩터링** 능력을 붙인다. 파일 전체 읽기와 문자열 검색에 의존하던 방식이 함수·클래스 단위로 바뀌면서, 대형 코드베이스에서 오류와 비용이 둘 다 줄어든다.

---

## 왜 쓰는지

AI 코딩 도구의 핵심 약점은 "코드를 많이 읽는 것"과 "코드를 정확히 이해하는 것"의 차이다. 일반 에이전트는 파일 검색·문자열 검색·전체 파일 읽기에 의존하는데, **작은 프로젝트에선 괜찮지만 코드베이스가 커질수록 비용과 오류 가능성이 같이 폭증**한다.

Serena는 공식 설명대로 "AI 코딩 에이전트를 위한 IDE"에 가깝다. 함수·클래스 같은 **심볼 단위**로 코드를 다룬다는 점이 핵심이다.

## 핵심 흐름

```
AI 에이전트 ── MCP ── Serena ── LSP ── 코드베이스
                                  │
                                  └─ find_symbol / find_referencing_symbols /
                                     find_declaration / find_implementations /
                                     get_diagnostics_for_file
```

### 일반 방식 vs Serena 방식

| 단계 | 일반 방식 | Serena 방식 |
|------|----------|------------|
| 1 | 파일명 검색 | 함수·클래스 심볼 검색 |
| 2 | 키워드 검색 | 심볼 선언 위치 확인 |
| 3 | 관련 파일 전체 읽기 | 참조하는 코드 추적 |
| 4 | 문자열 치환 | 필요한 메서드 본문만 교체 |
| 5 | 다시 테스트 | 진단(diagnostics) 정보 확인 |

## 핵심 용어 5개

| 용어 | 의미 | 관계 |
|------|------|------|
| **MCP** | Model Context Protocol | AI 클라이언트 ↔ 외부 도구 연결 프로토콜 |
| **Serena** | 오픈소스 코딩 에이전트 툴킷 | MCP 서버 형태로 AI 도구에 연결 |
| **LSP** | Language Server Protocol | IDE처럼 심볼·참조·진단 제공 |
| **Symbol** | 함수·클래스·메서드·변수 등 코드 구조 단위 | Serena의 작업 단위 |
| **Agent** | 작업을 계획하고 도구를 호출하는 AI | Serena 도구로 코드베이스 탐색·수정 |

Serena는 Claude Code, Codex, OpenCode, Gemini CLI 같은 터미널 클라이언트뿐 아니라 VSCode, Cursor, JetBrains 플러그인, Claude Desktop, OpenWebUI와도 MCP로 연결된다.

## 실습

### Claude Code에 연결

```bash
serena setup claude-code
# 사용자 범위
claude mcp add --scope user serena -- \
  serena start-mcp-server --context claude-code --project-from-cwd
# 프로젝트 단위
claude mcp add serena -- \
  serena start-mcp-server --context claude-code --project "$(pwd)"
```

### Codex에 연결

```bash
serena setup codex
```

수동 설정 (`~/.codex/config.toml`):

```toml
[mcp_servers.serena]
startup_timeout_sec = 15
command = "serena"
args = ["start-mcp-server", "--project-from-cwd", "--context=codex"]
```

### VSCode

```bash
serena start-mcp-server --context=vscode
serena start-mcp-server --context=vscode --project ${workspaceFolder}
```

### 동작 확인 체크리스트

설치보다 **"AI가 Serena 도구를 실제로 쓰고 있는가"**가 더 중요하다.

- MCP 클라이언트에서 Serena 서버 연결 상태
- 현재 프로젝트가 Serena에 활성화되어 있는지
- AI가 `grep`·전체 파일 읽기만 반복하고 있지는 않은지
- 심볼 검색·참조 추적·진단 도구를 실제로 호출하는지
- 변경 후 테스트·타입 체크를 실행하는지

세션이 길어지면 도구 사용이 흐려지는 문제가 알려져 있고, Serena 문서는 이를 보완하기 위한 hooks 설정을 안내한다.

## 모범사례 비교

| 패턴 | 장점 | 주의점 |
|------|------|--------|
| 일반 AI 코딩 | 설정 간단, 즉시 사용 | 큰 코드베이스에서 파일 전체 읽기·grep 폭증 → 오류 가능성 ↑ |
| Serena 단독 | 심볼 기반 탐색·참조 추적·리팩터링 강점 | 프로젝트 활성화·MCP 연결·LSP 상태 확인 필요 |
| Serena + IDE 백엔드 | IDE 코드 분석 능력을 AI가 활용 — 대형 프로젝트 유리 | IDE 프로젝트 루트와 Serena 루트가 일치해야 함, 설정 복잡 |
| Serena + 테스트 자동화 | 코드 수정 후 검증 루프 형성 | 테스트가 부실하면 효과 제한 |
| Serena + grep 병행 | 작은 수정 빠름, 구조적 수정은 Serena | 도구 중복으로 컨텍스트 비대 가능 |

## 마치며

Serena의 위치는 **"AI 모델을 더 좋게 만드는 도구"가 아니라 "AI가 코드베이스를 더 정확히 보게 만드는 도구"**다. 작은 토이 프로젝트보단 함수·클래스·참조 관계가 복잡한 실무 코드베이스에서 차이가 분명해진다. 설치만으로 자동 해결되지는 않으며, 프로젝트 활성화·MCP 연결 확인·테스트 루프까지 함께 갖춰야 실전 효과가 난다.

---

## 핵심 요약

- **문제 정의**: AI 에이전트는 파일 전체 읽기·grep에 의존 — 큰 코드베이스에서 오류 가능성이 높다
- **핵심 기능**: `find_symbol`·`find_referencing_symbols`·`find_declaration`·`get_diagnostics_for_file` 같은 심볼 단위 도구 제공
- **실행 조건**: MCP 연결 확인 + 프로젝트 활성화 + AI의 도구 사용 모니터링 + 테스트 루프 — 이 4가지가 함께 갖춰져야 실전 효과

## 참고

- 원문: [Serena MCP, AI 코딩이 왜 갑자기 더 똑똑해질까요](https://memoryhub.tistory.com/entry/%F0%9F%9A%80Serena-MCP-AI-%EC%BD%94%EB%94%A9%EC%9D%B4-%EC%99%9C-%EA%B0%91%EC%9E%90%EA%B8%B0-%EB%8D%94-%EB%98%91%EB%98%91%ED%95%B4%EC%A7%88%EA%B9%8C%EC%9A%94)
- [Serena 공식 문서](https://oraios.github.io/serena/)
