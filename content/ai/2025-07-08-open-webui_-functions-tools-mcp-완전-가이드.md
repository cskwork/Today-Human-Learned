+++
title = "Open WebUI: Functions, Tools, MCP 완전 가이드"
date = "2025-07-08"
description = "Open WebUI는 로컬에서 여러 AI 모델을 한 곳에서 쓸 수 있는 오픈소스 플랫폼이다. Functions, Tools, MCP 세 가지 확장 방법의 역할이 다르며, 목적에 맞게 골라 써야 한다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Open WebUI는 로컬에서 여러 AI 모델을 한 곳에서 쓸 수 있는 오픈소스 플랫폼이다. Functions, Tools, MCP 세 가지 확장 방법의 역할이 다르며, 목적에 맞게 골라 써야 한다.

---

## Open WebUI를 왜 쓰는지 감 잡기

ChatGPT처럼 AI와 대화하되, 데이터가 외부로 나가지 않기를 원한다면 Open WebUI가 답이다. 자체 서버에 설치하면 Ollama, OpenAI, Anthropic, Gemini 등 다양한 모델을 하나의 인터페이스에서 쓸 수 있다. 오프라인 환경에서도 작동한다.

여기에 Functions, Tools, MCP를 조합하면 웹 검색, 데이터베이스 연동, 외부 API 호출까지 확장된다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 로컬 설치 → 모델 연결 → 확장(Functions / Tools / MCP) → 사용`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Open WebUI | Ollama 등 로컬 AI 모델을 ChatGPT처럼 웹 브라우저에서 쓸 수 있게 해주는 오픈소스 플랫폼 |
| Functions | Open WebUI 서버 내부에서 실행되는 플러그인; 플랫폼 자체 동작을 바꾼다 |
| Tools | LLM이 대화 중에 필요할 때 직접 실행하는 Python 스크립트; 외부 데이터·API에 접근한다 |
| MCP | AI와 외부 시스템을 잇는 표준 프로토콜(Anthropic 설계); "AI용 USB 포트"라고 이해하면 된다 |
| MCPO | MCP 서버를 Open WebUI가 이해할 수 있는 OpenAPI 형식으로 변환해주는 프록시 |

## 예를 들어 설명하면

세 가지 확장 방법의 차이를 한 줄로 구분하면:

| 방법 | 실행 주체 | 주요 용도 |
|---|---|---|
| Functions | Open WebUI 서버 | 플랫폼 UI 변경, 새 모델 제공업체 추가, 메시지 필터링 |
| Tools | LLM이 요청 시 호출 | 실시간 웹 검색, 날씨·주식 조회, DB 쿼리 |
| MCP | 외부 MCP 서버 | 복잡한 외부 시스템 통합, 표준화된 리소스·툴·프롬프트 제공 |

Docker로 Open WebUI를 설치하는 최소 명령:

```bash
docker run -d -p 3000:8080 --name open-webui --restart always \
  ghcr.io/open-webui/open-webui:main
```

MCP를 Open WebUI에 연결하려면 mcpo 프록시를 먼저 실행한다.

```bash
uvx mcpo --port 8000 --api-key "your-key" -- your_mcp_server_command
```

그 뒤 Open WebUI 설정에서 `http://localhost:8000`을 도구 서버로 등록한다.

## 이 단계에서 중요한 판단 기준

플랫폼 동작 자체를 바꾸려면 Functions, 대화 중 실시간 외부 데이터가 필요하면 Tools, 복잡한 외부 시스템을 표준화된 방식으로 연결하려면 MCP를 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Open WebUI의 확장 3형제(Functions·Tools·MCP)는 각각 플랫폼 제어, LLM 실시간 기능 추가, 외부 시스템 통합이라는 서로 다른 문제를 푼다.**

## 나중에 더 깊게 들어가면

- Pipe / Filter / Action Functions 각각의 구현 패턴
- MCP 서버를 Python으로 직접 작성하는 방법
- Open WebUI + Ollama + GPU 가속 조합으로 로컬 추론 최적화

---

**원본:** [Open WebUI: Functions, Tools, MCP 완전 가이드](https://memoryhub.tistory.com/721)
