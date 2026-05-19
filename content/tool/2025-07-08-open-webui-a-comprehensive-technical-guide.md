+++
title = "Open WebUI: 로컬 AI 채팅 인터페이스 완전 가이드"
date = "2025-07-08"
description = "Open WebUI는 Ollama나 OpenAI 등 어떤 LLM에도 연결할 수 있는 자체 호스팅 채팅 UI로, Tools/Functions/MCP 세 가지 방식으로 모델의 능력을 확장할 수 있다."
tags = ["tool"]
categories = ["tool"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Open WebUI는 Ollama나 OpenAI 등 어떤 LLM에도 연결할 수 있는 자체 호스팅 채팅 UI로, Tools/Functions/MCP 세 가지 방식으로 모델의 능력을 확장할 수 있다.

---

## Open WebUI를 왜 쓰는지 감 잡기

ChatGPT처럼 생긴 채팅 인터페이스를 내 서버나 노트북에서 직접 돌리고 싶을 때 Open WebUI를 쓴다. 데이터가 외부로 나가지 않고, 로컬 GPU에 올린 모델(Llama, Mistral 등)이나 OpenAI/Claude 같은 원격 API를 같은 UI에서 골라 쓸 수 있다.

특이한 점은 확장성이다. 모델에게 "지금 시간 알려줘"라고 물으면 AI가 직접 시스템 시계를 읽어 답하게 만들 수 있다. 이런 기능을 Tools, Functions, MCP 세 가지 방식으로 추가한다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: 사용자 질문 → Open WebUI → LLM → Tool 호출 → 결과 반환 → 최종 답변`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Ollama | 로컬 PC에서 오픈소스 LLM을 실행해주는 런타임. Open WebUI와 자주 함께 쓴다. |
| Tool | LLM이 스스로 호출할 수 있는 Python 함수 묶음. 계산기, 날씨 조회 같은 역할. |
| Function | Open WebUI UI 자체에 기능을 추가하는 Python 스크립트. 버튼, 필터, 새 모델 백엔드 등. |
| MCP | AI 에이전트가 외부 도구와 표준 방식으로 통신하는 프로토콜. |
| mcpo | MCP 서버를 Open WebUI가 이해하는 OpenAPI 형식으로 변환해주는 래퍼 도구. |

## 예를 들어 설명하면

현재 시간을 돌려주는 Tool을 직접 만들어 등록하는 과정이다.

```python
# time_tool.py
from datetime import datetime
from openwebui.tools import tool

class GetTime(tool.Tool):
    name = "get_time"
    description = "현재 시간을 ISO-8601 형식으로 반환합니다."

    def run(self) -> str:
        return datetime.now().isoformat()
```

등록 경로: Workspace > Tools > + Add > 코드 붙여넣기 > 저장  
채팅에서 + 아이콘으로 `get_time` Tool을 활성화하면, "지금 몇 시야?"라고 물을 때 모델이 자동으로 이 함수를 호출한다.

## 이 단계에서 중요한 판단 기준

| 필요한 것 | 선택 |
|---|---|
| 모델이 실시간 데이터를 가져와야 할 때 | Tool |
| UI에 버튼이나 새 모델 백엔드를 추가할 때 | Function |
| 기존 MCP 서버를 재사용하거나 격리가 필요할 때 | MCP + mcpo |

## 한 줄 요약 — 이것만 기억하면 된다

**Open WebUI는 어떤 LLM에도 붙는 자체 호스팅 채팅 UI이며, Tool/Function/MCP로 모델의 행동 범위를 Python 코드 수준에서 확장할 수 있다.**

## 나중에 더 깊게 들어가면

- Pipe Function으로 여러 모델의 응답을 합쳐 하이브리드 RAG 구성하기
- Filter Function으로 모든 대화에 시스템 프롬프트 자동 삽입하기
- Kubernetes Helm 차트로 멀티유저 클러스터 배포하기

---

**원본:** [Open WebUI: A Comprehensive Technical Guide — https://memoryhub.tistory.com/722](https://memoryhub.tistory.com/722)
