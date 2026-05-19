+++
title = "Vibe Coding과 LangGraph, llms.txt, MCP — AI 주도 앱 개발의 새로운 패러다임"
date = "2025-03-21"
description = "Vibe Coding은 \"무엇을 원하는지\"를 자연어로 설명하고 AI가 코드를 짜게 하는 개발 방식이며, LangGraph는 그 흐름을 그래프로 구조화하고, MCP와 llms.txt는 AI가 외부 데이터와 도구에 접근하는 방식을 표준화한다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Vibe Coding은 "무엇을 원하는지"를 자연어로 설명하고 AI가 코드를 짜게 하는 개발 방식이며, LangGraph는 그 흐름을 그래프로 구조화하고, MCP와 llms.txt는 AI가 외부 데이터와 도구에 접근하는 방식을 표준화한다.

---

## 왜 쓰는지 감 잡기

전통적인 개발은 구체적인 문법과 세부 구현을 개발자가 직접 작성한다. Vibe Coding은 반대 방향으로 출발한다. 개발자가 "고객 요청을 분석해서 지식베이스에서 답을 찾고, 없으면 상담원에게 넘겨줘"처럼 의도를 설명하면, AI가 실제 코드를 생성한다. 여기에 여러 AI 에이전트가 협력하는 워크플로우를 구조화하는 LangGraph, AI가 웹사이트 정보를 읽는 표준인 llms.txt, 외부 도구를 연결하는 MCP가 결합되면 복잡한 AI 시스템을 빠르게 만들 수 있다.

핵심 흐름:

`의도 설명 (Vibe Coding) → 워크플로우 구조화 (LangGraph) → 외부 도구 연결 (MCP) → 문서 접근성 확보 (llms.txt)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Vibe Coding | 코드 대신 의도를 자연어로 설명하고 AI에게 구현을 맡기는 개발 방식. |
| LangGraph | AI 워크플로우를 노드(실행 단계)와 엣지(흐름 제어)로 표현하는 그래프 기반 프레임워크. |
| llms.txt | 웹사이트가 LLM에게 핵심 정보를 구조화해서 제공하는 마크다운 파일 규약. |
| MCP (Model Context Protocol) | LLM이 외부 데이터베이스·API·도구와 통신하는 방식을 표준화한 프로토콜. |
| StateGraph | LangGraph의 핵심 클래스. 에이전트가 공유하는 상태(state)와 처리 흐름을 정의한다. |

## 예를 들어 설명하면

LangGraph로 고객 지원 에이전트 워크플로우를 만드는 최소 구조다.

```python
from langgraph.graph import StateGraph
from typing import TypedDict, List, Dict

class AgentState(TypedDict):
    messages: List
    customer_info: Dict

workflow = StateGraph(AgentState)

# 각 노드는 하나의 처리 단계
workflow.add_node("parse_intent", parse_intent_fn)
workflow.add_node("search_knowledge_base", search_knowledge_base_fn)
workflow.add_node("generate_response", generate_response_fn)

# 엣지는 실행 순서
workflow.add_edge("parse_intent", "search_knowledge_base")
workflow.add_edge("search_knowledge_base", "generate_response")

agent = workflow.compile()
```

이 구조를 Vibe Coding 방식으로 AI에게 설명했을 때, AI가 위와 같은 코드를 생성하도록 유도한다.

## 이 단계에서 중요한 판단 기준

Vibe Coding은 명확한 의도 전달이 생명이다. "좋은 에이전트 만들어줘"가 아니라 "고객 요청을 분류하고, 분류 결과에 따라 FAQ 검색 또는 상담원 연결 중 하나를 선택하는 워크플로우"처럼 구체적으로 설명해야 AI가 올바른 코드를 생성한다.

## 주의할 점

- AI 생성 코드는 반드시 검토한다. 특히 인증, 데이터 처리, 오류 처리 부분.
- 복잡한 로직은 한 번에 설명하지 말고 작은 단계로 나눠서 Vibe Coding한다.
- 테스트 자동화 없이 AI 생성 코드를 프로덕션에 올리지 않는다.

## 한 줄 요약 — 이것만 기억하면 된다

**Vibe Coding은 "무엇을"에 집중하고 LangGraph는 "어떻게 흐를지"를 구조화하며 MCP는 "어디서 데이터를 가져올지"를 표준화한다 — 세 가지가 합쳐지면 AI 시스템 개발 속도가 크게 빨라진다.**

## 나중에 더 깊게 들어가면

- LangGraph Studio로 에이전트 워크플로우를 시각적으로 디버깅하기
- llms.txt 파일을 자신의 웹사이트에 추가해 AI 접근성 높이기
- MCP 클라이언트를 직접 구현해 사내 데이터베이스에 LLM 연결하기

---

**원본:** [Vibe Coding LangGraph Apps with llms.txt and MCP - AI 주도 앱 개발의 새로운 패러다임](https://memoryhub.tistory.com/489)
