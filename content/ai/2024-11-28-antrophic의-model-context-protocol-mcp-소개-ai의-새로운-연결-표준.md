+++
title = "MCP — AI와 외부 도구를 연결하는 표준 프로토콜"
date = "2024-11-28"
description = "MCP는 AI 모델이 파일 시스템, 데이터베이스, API 같은 외부 데이터 소스에 표준화된 방식으로 접근할 수 있는 Anthropic의 오픈 프로토콜이다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> MCP는 AI 모델이 파일 시스템, 데이터베이스, API 같은 외부 데이터 소스에 표준화된 방식으로 접근할 수 있는 Anthropic의 오픈 프로토콜이다.

---

## 이 주제를 왜 쓰는지 감 잡기

AI 어시스턴트는 학습 데이터 안에 있는 것만 안다. 회사 내부 문서, 실시간 데이터베이스, 사용자의 로컬 파일은 모른다. 이 간극을 메우려면 AI가 외부 시스템에 접근할 수 있어야 한다. 그런데 데이터 소스마다 연결 방식이 다 다르면, 새로운 소스를 추가할 때마다 커스텀 코드가 필요하다.

MCP는 이 문제를 USB처럼 표준화해서 해결한다. 데이터 소스 쪽에서 MCP 서버를 구현하고, AI 도구 쪽에서 MCP 클라이언트를 구현하면, 어떤 조합이든 같은 방식으로 연결된다.

`핵심 흐름: AI 클라이언트 → MCP 클라이언트 → MCP 서버 → 외부 데이터 소스`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| MCP 서버 | 특정 데이터 소스(파일 시스템, DB, API 등)를 AI가 읽고 쓸 수 있게 노출하는 프로세스 |
| MCP 클라이언트 | Claude Desktop 같은 AI 도구 내부에서 MCP 서버와 통신하는 컴포넌트 |
| 도구(Tool) | MCP 서버가 AI에게 제공하는 기능 단위. 예: "파일 읽기", "쿼리 실행" |
| 리소스(Resource) | AI가 참고할 수 있도록 MCP 서버가 노출하는 데이터. 예: 특정 파일 내용, 테이블 스키마 |
| 표준화(Standardization) | 연결 방식을 하나로 고정해서 새 소스를 추가해도 클라이언트 코드를 바꾸지 않아도 되는 상태 |

## 예를 들어 설명하면

MCP 이전에는 AI가 Slack 메시지를 읽으려면 Slack API 호출 코드를, Google Drive 파일을 읽으려면 Drive API 코드를 각각 따로 만들어야 했다. MCP 이후에는 각 서비스가 MCP 서버를 제공하면, AI 클라이언트는 동일한 MCP 프로토콜로 두 서비스 모두와 통신한다.

```
MCP 없을 때:
AI → Slack 전용 코드 → Slack
AI → Drive 전용 코드 → Drive

MCP 있을 때:
AI (MCP 클라이언트) → [MCP 프로토콜] → Slack MCP 서버 → Slack
                                        → Drive MCP 서버 → Drive
```

Claude Desktop은 로컬 MCP 서버를 설정 파일에 등록하면 바로 사용할 수 있다.

## 이 단계에서 중요한 판단 기준

MCP 서버를 직접 구현할 때는 "읽기 전용으로 충분한지, 쓰기 권한까지 줄 것인지"를 먼저 결정하라. 쓰기 권한은 실수나 악의적 사용 시 데이터 손상으로 이어진다.

## 한 줄 요약 — 이것만 기억하면 된다

**MCP는 AI가 외부 시스템과 대화하는 방식을 하나로 통일한 오픈 표준이다.**

## 나중에 더 깊게 들어가면

- MCP SDK를 사용한 커스텀 서버 구현 (Python/TypeScript)
- MCP의 인증/권한 처리 방식
- 로컬 MCP 서버와 원격 MCP 서버의 차이와 보안 고려사항

---

**원본:** [Antrophic의 Model Context Protocol (MCP) 소개: AI의 새로운 연결 표준](https://memoryhub.tistory.com/410)
