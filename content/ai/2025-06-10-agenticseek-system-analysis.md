+++
title = "AgenticSeek — 완전 로컬 멀티 에이전트 플랫폼 구조 분석"
date = "2025-06-10"
description = "AgenticSeek는 외부 클라우드 없이 로컬에서만 동작하는 멀티 에이전트 시스템으로, ML 기반 라우터가 요청을 전문화된 에이전트에 분배하고 FastAPI 백엔드가 전체를 조율한다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> AgenticSeek는 외부 클라우드 없이 로컬에서만 동작하는 멀티 에이전트 시스템으로, ML 기반 라우터가 요청을 전문화된 에이전트에 분배하고 FastAPI 백엔드가 전체를 조율한다.

---

## AgenticSeek를 왜 쓰는지 감 잡기

ChatGPT나 Claude.ai 같은 상용 AI 어시스턴트는 사용자 입력이 외부 서버로 전송된다. 기업 내부 코드나 민감한 데이터를 다루는 개발자에게는 이 점이 걸린다.

AgenticSeek는 모든 처리를 사용자 머신 안에서 끝낸다. API 비용도 없고, 데이터가 밖으로 나가지 않는다. 대신 로컬 LLM과 컨테이너를 직접 운영해야 하는 부담이 따른다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: 사용자 입력 → FastAPI → AgentRouter(ML) → 전문 에이전트 → 도구 실행 → 결과 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| AgentRouter | 사용자 요청을 분석해 어떤 에이전트가 처리할지 결정하는 ML 기반 분류기 (BART + 커스텀 LLM) |
| PlannerAgent | 복잡한 작업을 JSON 단위 하위 태스크로 쪼개는 계획 담당 에이전트 |
| Tool Execution Framework | 에이전트가 코드 실행, 웹 검색, 파일 조작 등을 수행하는 플러그인 방식의 도구 계층 |
| SearXNG | 외부 검색 엔진에 의존하지 않는 자체 호스팅 메타 검색 엔진 — 프라이버시 웹 검색용 |
| Redis Cache | 세션 상태와 검색 결과를 빠르게 저장하는 인메모리 저장소 |

## 예를 들어 설명하면

"Python으로 CSV 파싱 스크립트 짜줘"라는 요청이 들어오면 이렇게 처리된다.

```
사용자 입력
  └─> FastAPI (Port 8000)
        └─> AgentRouter (코딩 태스크 판별)
              └─> CoderAgent
                    └─> Tool: Python 인터프리터
                          └─> 코드 실행 + 결과 반환
React UI (Port 3000)에 결과 표시
```

에이전트는 계층 구조를 가진다. `PlannerAgent`가 먼저 태스크를 분해하면, `CoderAgent`, `BrowserAgent`, `FileAgent` 등이 각 하위 태스크를 담당한다. 모든 에이전트는 Template Method 패턴으로 공통 인터페이스를 공유한다.

## 이 단계에서 중요한 판단 기준

AgenticSeek를 도입할 때 핵심 판단 기준은 하나다 — 데이터가 절대 외부로 나가면 안 되는 환경인가? 그렇다면 로컬 GPU와 운영 부담을 감수할 가치가 있다.

## 한 줄 요약 — 이것만 기억하면 된다

**AgenticSeek는 ML 라우터가 요청을 전문 에이전트에 분배하는 완전 로컬 멀티 에이전트 플랫폼이다 — 프라이버시가 최우선일 때 선택한다.**

## 나중에 더 깊게 들어가면

- 프로덕션 환경을 위한 SSL/TLS, Prometheus 모니터링, Kubernetes 수평 확장
- HashiCorp Vault 같은 외부 시크릿 스토어 연동
- 새 에이전트 타입을 플러그인 방식으로 추가하는 방법

---

**원본:** [AgenticSeek - System Analysis](https://memoryhub.tistory.com/679)
