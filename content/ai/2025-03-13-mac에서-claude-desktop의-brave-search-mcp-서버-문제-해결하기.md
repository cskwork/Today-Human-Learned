+++
title = "Mac에서 Claude Desktop의 Brave Search MCP 서버 문제 해결하기"
date = "2025-03-13"
description = "Mac에서 Brave Search MCP가 동작하지 않는 원인은 대부분 Claude Desktop이 `npx`의 경로를 찾지 못하는 것이므로, 설정 파일에 절대경로를 명시하면 해결된다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Mac에서 Brave Search MCP가 동작하지 않는 원인은 대부분 Claude Desktop이 `npx`의 경로를 찾지 못하는 것이므로, 설정 파일에 절대경로를 명시하면 해결된다.

---

## 왜 이 문제가 생기는지 감 잡기

Claude Desktop은 MCP 서버를 시작할 때 시스템 터미널과 다른 환경에서 실행된다. 터미널에서는 `npx`가 잘 동작해도, Claude Desktop은 `~/.zshrc`나 `~/.bash_profile`에 설정된 PATH를 읽지 못하는 경우가 많다. nvm으로 Node.js를 설치했다면 특히 자주 발생한다.

핵심 흐름:

`Node.js 경로 확인 → Brave API 키 발급 → config.json에 절대경로 지정 → Claude 재시작`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| MCP 서버 | Claude가 외부 서비스를 호출하기 위한 중간 프로그램. `npx`로 실행된다. |
| nvm | Node.js 버전 관리자. 여러 Node.js 버전을 전환하며 쓸 수 있게 해준다. |
| PATH | 운영체제가 명령어를 찾을 디렉토리 목록. 여기 없으면 명령어를 찾지 못한다. |
| ENOENT | "파일 없음" 오류 코드. `spawn npx ENOENT`는 npx를 찾지 못했다는 뜻이다. |
| claude_desktop_config.json | Claude Desktop이 MCP 서버 목록을 읽는 JSON 설정 파일. |

## 예를 들어 설명하면

먼저 npx의 실제 경로를 확인한다.

```bash
which npx
# 출력 예: /Users/내이름/.nvm/versions/node/v23.3.0/bin/npx

which node
# 출력 예: /Users/내이름/.nvm/versions/node/v23.3.0/bin/node
```

그 경로를 `claude_desktop_config.json`에 그대로 쓴다.

```json
{
  "mcpServers": [
    {
      "name": "brave-search",
      "command": "/Users/내이름/.nvm/versions/node/v23.3.0/bin/npx",
      "args": ["-y", "@modelcontextprotocol/server-brave-search"],
      "env": {
        "BRAVE_API_KEY": "발급받은_API_키",
        "PATH": "/Users/내이름/.nvm/versions/node/v23.3.0/bin:/usr/local/bin:/usr/bin:/bin",
        "NODE_PATH": "/Users/내이름/.nvm/versions/node/v23.3.0/lib/node_modules"
      }
    }
  ]
}
```

## Brave Search API 키 발급

1. [brave.com/search/api](https://brave.com/search/api/) 에서 개발자 계정 생성 또는 로그인
2. API 키 생성 후 복사
3. 위 JSON의 `BRAVE_API_KEY` 값으로 붙여넣기

## 이 단계에서 중요한 판단 기준

`command` 필드에 `"npx"`만 쓰면 Claude Desktop이 경로를 못 찾아 실패하므로, 반드시 `which npx` 출력값 전체를 절대경로로 입력해야 한다.

## 자주 겪는 오류와 해결책

| 오류 | 원인 | 해결 |
|---|---|---|
| `spawn npx ENOENT` | npx 경로를 찾지 못함 | `command`에 절대경로 지정 |
| API 키 관련 오류 | 키가 잘못됨 또는 누락됨 | Brave 콘솔에서 새 키 발급 |
| MCP 서버 시작 실패 | 패키지 설치 문제 | `npm install -g @modelcontextprotocol/server-brave-search` 재실행 |
| 설정 파일 인식 못함 | 파일 위치 오류 또는 JSON 문법 오류 | 경로와 JSON 문법 재확인 |

설정 파일 경로: `~/Library/Application Support/Claude/claude_desktop_config.json`

파일이 없으면 직접 생성한다.

```bash
mkdir -p ~/Library/Application\ Support/Claude/
touch ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

## 한 줄 요약 — 이것만 기억하면 된다

**`which npx` 결과 전체를 `command` 필드에 절대경로로 쓰고, `PATH`와 `NODE_PATH`도 env에 함께 명시하면 대부분 해결된다.**

## 나중에 더 깊게 들어가면

- Claude Desktop Developer Mode 로그로 MCP 오류 상세 확인하기 (`Menu > Help > Enable Developer Mode`)
- Homebrew로 Node.js를 설치했을 때의 경로 구조와 nvm 경로의 차이
- 여러 MCP 서버를 동시에 등록할 때 `mcpServers` 배열 관리

---

**원본:** [Mac에서 Claude Desktop의 Brave Search MCP 서버 문제 해결하기](https://memoryhub.tistory.com/471)
