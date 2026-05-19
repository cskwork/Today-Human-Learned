+++
title = "Desktop Commander MCP — AI로 데스크톱 자동화하기"
date = "2025-05-28"
description = "Desktop Commander MCP는 Claude가 파일 시스템과 터미널에 직접 접근할 수 있게 해주는 MCP 서버로, 반복적인 시스템 작업을 자연어 명령으로 처리할 수 있게 한다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Desktop Commander MCP는 Claude가 파일 시스템과 터미널에 직접 접근할 수 있게 해주는 MCP 서버로, 반복적인 시스템 작업을 자연어 명령으로 처리할 수 있게 한다.

---

## 왜 쓰는지 감 잡기

AI 어시스턴트에게 "이 프로젝트 테스트 실행하고 결과 정리해줘"라고 말하면 실제로 실행해주면 좋겠지만, 기본 상태의 Claude Desktop은 대화만 할 수 있고 시스템에 직접 접근하지 못한다.

Desktop Commander MCP는 이 한계를 없앤다. MCP(Model Context Protocol)는 AI 앱과 외부 시스템을 연결하는 표준 프로토콜이다. Desktop Commander는 이 프로토콜을 구현한 서버로, Claude Desktop과 로컬 컴퓨터 사이를 연결한다. 설정 후에는 Claude가 파일을 읽고 쓰고, 터미널 명령을 실행하고, 코드를 수정하는 작업을 직접 수행한다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: 사용자 자연어 요청 → Claude Desktop → Desktop Commander(MCP 서버) → 로컬 파일/터미널`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| MCP(Model Context Protocol) | AI 앱이 외부 도구나 데이터 소스와 통신하는 표준 규격. Anthropic이 2024년 오픈소스로 공개 |
| MCP 서버 | 특정 기능(파일 접근, DB 조회 등)을 MCP 규격으로 노출하는 경량 프로그램 |
| MCP 호스트 | MCP 클라이언트를 내장한 AI 앱. Claude Desktop이 대표적 |
| allowedDirectories | Desktop Commander가 접근을 허용하는 폴더 목록. 보안 설정의 핵심 |
| blockedCommands | 실행을 차단할 시스템 명령어 목록. `rm -rf` 같은 위험한 명령을 방지 |

## 예를 들어 설명하면

Desktop Commander를 설치한 뒤 Claude Desktop 설정 파일에 아래를 추가한다.

```json
{
  "allowedDirectories": ["/Users/myname/projects"],
  "blockedCommands": ["rm -rf", "format"]
}
```

이 설정으로 Claude는 `projects` 폴더 안에서만 파일을 읽고 쓸 수 있고, 위험한 명령어는 실행하지 못한다. 이후 Claude Desktop에서 "프로젝트 루트의 모든 Python 파일에서 TODO 주석을 찾아줘"라고 요청하면, Desktop Commander가 `search_files()`를 실행하고 결과를 Claude에게 돌려준다.

## 이 단계에서 중요한 판단 기준

Desktop Commander를 쓰기 전에 반드시 `allowedDirectories`와 `blockedCommands`를 설정하라 — 설정 없이는 Claude가 시스템 전체에 접근할 수 있다.

## 한 줄 요약 — 이것만 기억하면 된다

**Desktop Commander MCP는 Claude에게 로컬 파일 시스템과 터미널 접근 권한을 부여하되, 접근 범위를 명시적으로 제한해야 안전하게 사용할 수 있다.**

## 나중에 더 깊게 들어가면

- MCP 서버 직접 구현 — Python/TypeScript SDK로 나만의 도구 서버 만들기
- Desktop Commander의 `edit_block` 도구로 코드 정밀 수정하는 방법
- 여러 MCP 서버를 동시에 연결해 복합 워크플로우 구성하기

---

**원본:** [Desktop Commander MCP - 데스크톱 자동화의 새로운 패러다임](https://memoryhub.tistory.com/598)
